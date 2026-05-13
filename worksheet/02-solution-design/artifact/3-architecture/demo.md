---
artifact: 3 — Demo kiến trúc dữ liệu
format: sơ đồ xử lý + bảng thành phần
---

# demo.md — Demo kiến trúc dữ liệu

Demo này minh hoạ cách kiến trúc dữ liệu chặn rủi ro `U-03`: AI kết luận nên cắt khoản nào dù dữ liệu tháng còn thiếu hoặc chưa xác nhận.

---

## 1. Sơ đồ cách hệ thống xử lý

```text
┌──────────────────────────────┐
│  USER / APP REPORT SCREEN    │
│  "Tôi nên cắt khoản nào?"    │
└──────────────┬───────────────┘
               │
               ▼
┌────────────────────────────────────────┐
│ CHAT API / ORCHESTRATOR               │
│ - auth, rate limit, request id        │
│ - log raw query + user_id_hashed      │
└──────────────┬─────────────────────────┘
               │
               ▼
┌────────────────────────────────────────┐
│ RISK ROUTER / INTENT CHECK            │
│ - detect "cắt khoản nào", "tiền đâu"  │
│ - classify: normal / risky / OOS      │
└───────┬───────────────────┬────────────┘
        │ risky             │ OOS
        ▼                   ▼
┌──────────────────────┐   ┌──────────────────────┐
│ DATA READINESS GATE  │   │ REFUSE OOS           │
│ - đủ nguồn chưa?     │   │ - không trả lời      │
│ - thiếu tiền mặt?    │   │ - suggest scope đúng │
│ - còn dòng chưa x.n? │   │ - log: refuse_oos    │
└───────┬──────────────┘   └──────────────────────┘
        │
        ├──────────── missing critical data ────────────┐
        │                                               │
        ▼                                               ▼
┌──────────────────────────────┐             ┌──────────────────────────┐
│ REFUSE + REVIEW FLOW         │             │ HUMAN SUPPORT CHANNEL    │
│ - "chưa thể kết luận"        │             │ - support / reviewer     │
│ - ask: nhập thêm dữ liệu     │             │ - dùng cho case khẩn     │
│ - log: missing_cash/...      │             │ - log: handoff_count     │
└──────────────────────────────┘             └──────────────────────────┘
        ▲
        │ ready
        │
        ▼
┌────────────────────────────────────────┐
│ TRANSACTION AGGREGATOR                 │
│ - tách 3 lớp dữ liệu:                 │
│   1) confirmed store                  │
│   2) imported_unconfirmed store       │
│   3) missing_source flags             │
└───────┬──────────────┬─────────────────┘
        │              │
        │              └──────► OCR QUALITY CHECK
        │                        - low_confidence? flag
        │                        - log: ocr_low_conf
        ▼
┌────────────────────────────────────────┐
│ SAFE SUMMARY CACHE                     │
│ Redis / in-memory                      │
│ TTL: 6h cho monthly summary            │
│ Key = user + month + data_version      │
└───────┬────────────────────────────────┘
        │ hit
        │──────────────────────► return cached safe summary
        │
        │ miss
        ▼
┌────────────────────────────────────────┐
│ POLICY + PROMPT BUILDER               │
│ - inject data status                  │
│ - "không kết luận nếu thiếu nguồn"    │
│ - pass confirmed_count/unconfirmed    │
└───────┬────────────────────────────────┘
        ▼
┌────────────────────────────────────────┐
│ LLM RESPONSE GENERATOR                 │
│ - chỉ đọc structured summary           │
│ - không đọc raw OCR mờ trực tiếp       │
│ - output: insight + confidence label   │
└───────┬────────────────────────────────┘
        ▼
┌────────────────────────────────────────┐
│ OUTPUT GUARD                           │
│ - block if tries strong recommendation │
│   while data_status != complete        │
│ - add badges + next step               │
│ - log: blocked_overclaim               │
└───────┬──────────────────────┬─────────┘
        │                      │
        ▼                      ▼
┌──────────────────────┐   ┌──────────────────────────┐
│ SEND TO UI           │   │ LOGS / METRICS / ALERTS  │
│ - default / uncertain│   │ - no_data_rate           │
│ - refuse / handoff   │   │ - refuse_rate            │
└──────────────────────┘   │ - handoff_count          │
                           │ - blocked_overclaim      │
                           │ - ocr_low_confidence     │
                           └──────────┬───────────────┘
                                      ▼
                           ┌──────────────────────────┐
                           │ MONITORING DASHBOARD     │
                           │ Alert if spike > threshold│
                           └──────────────────────────┘
```

---

## 2. Thành phần chính

| Thành phần | Nhận gì? | Làm gì? | Trả ra gì? |
|---|---|---|---|
| Risk router / intent check | Câu hỏi user ở tab báo cáo tháng | Nhận diện đây có phải câu hỏi rủi ro cao, pressure-trap, hay out-of-scope không | Route sang `data readiness gate` hoặc `refuse OOS` |
| Data readiness gate | Query + metadata của tháng hiện tại | Kiểm tra còn thiếu nguồn quan trọng không: tiền mặt, ví điện tử, giao dịch chưa import, giao dịch chưa xác nhận | `ready`, `missing_critical_data`, hoặc `handoff` |
| Transaction aggregator | Confirmed store, imported_unconfirmed store, missing-source flags | Tổng hợp dữ liệu thành summary có cấu trúc để model dùng mà không phải tự đoán từ raw data | `structured_summary` + `data_status` |
| OCR quality check | Ảnh sao kê / kết quả OCR | Gắn cờ `low_confidence` nếu ảnh mờ, số tiền không chắc, merchant không rõ | `ocr_ok` hoặc `ocr_low_confidence` |
| Safe summary cache | user_id + month + version dữ liệu | Cache monthly summary an toàn để giảm latency và tránh tính lại nhiều lần | Cached summary hoặc cache miss |
| Policy + prompt builder | Structured summary + data status | Đóng gói rule “không kết luận nếu thiếu nguồn” trước khi gọi model | Prompt an toàn + context đã chuẩn hóa |
| LLM response generator | Prompt an toàn + structured summary | Tạo insight có điều kiện thay vì phán quyết cuối cùng | Draft response + confidence label |
| Output guard | Draft response + data status | Chặn câu trả lời overclaim, thêm badge và next step phù hợp | Response an toàn cho UI hoặc refuse |
| Logs / metrics / alerts | Query, status code, reason code, latency | Ghi lại lỗi lặp lại và phát cảnh báo khi pattern bất thường tăng | Dashboard, alert, audit trail |

---

## 3. Khi hệ thống gặp vấn đề

| Khi nào lỗi xảy ra? | Hệ thống làm gì? | Người dùng thấy gì? |
|---|---|---|
| Thiếu nguồn chính như tiền mặt / ví điện tử / giao dịch chưa import | `Data readiness gate` chặn trước khi gọi LLM, trả `missing_critical_data`, log reason code | Màn hình `chưa thể kết luận`, kèm nút `Nhập thêm dữ liệu thiếu` |
| Còn nhiều giao dịch chưa xác nhận hoặc OCR mờ | Chuyển sang `review flow`, không cho model chốt recommendation, tăng metric `need_user_confirmation` hoặc `ocr_low_confidence` | Màn hình `cần kiểm tra thêm`, kèm `Xác nhận từng khoản` hoặc `Gọi hỗ trợ viên` |
| Cache miss + dữ liệu gốc không đủ sạch | Tính lại summary từ confirmed store; nếu vẫn không đủ thì refuse thay vì đoán | User thấy insight chưa sẵn sàng, không thấy câu trả lời chắc chắn |
| Câu hỏi vượt phạm vi AI như đầu tư coin | Router trả về `refuse OOS`, không vào path summarize | Màn hình từ chối ngắn gọn và hướng user quay về đúng phạm vi |
| Output guard phát hiện model overclaim | Chặn response, thay bằng wording an toàn, log `blocked_overclaim` | User thấy câu “chưa thể kết luận từ dữ liệu hiện có” thay vì recommendation mạnh |
| `missing_critical_data` hoặc `blocked_overclaim` tăng bất thường | Monitoring tạo alert cho nhóm sản phẩm / kỹ thuật kiểm tra regression | User không thấy phần monitoring, nhưng hệ thống được chỉnh sớm hơn ở vòng sau |

---

## 4. Kiểm tra nhanh

- [x] Sơ đồ không chỉ là “AI trả lời tốt hơn”, mà có bước kiểm tra cụ thể.
- [x] Có cách xử lý khi thiếu dữ liệu.
- [x] Có cách chuyển sang người thật.
- [x] Có cách theo dõi để lần sau sửa tốt hơn.

## 5. Metrics nên theo dõi sau launch

- `missing_critical_data_rate`: tỷ lệ query bị chặn vì thiếu tiền mặt / ví điện tử / dữ liệu chưa import.
- `need_user_confirmation_rate`: tỷ lệ query bị trả về vì còn quá nhiều giao dịch chưa xác nhận.
- `blocked_overclaim_count`: số lần output guard chặn câu trả lời quá chắc.
- `handoff_count`: số lần hệ thống phải chuyển sang người thật.
- `ocr_low_confidence_rate`: tỷ lệ ảnh sao kê bị đánh cờ mờ / không chắc.

## 6. 2 single points of failure cần để ý

- **Data readiness gate**: nếu rule ở đây sai, toàn bộ hệ thống hoặc quá permissive hoặc quá refuse.
- **Transaction aggregator**: nếu gom sai giữa `confirmed` và `unconfirmed`, mọi layer phía sau đều xây trên dữ liệu sai.
