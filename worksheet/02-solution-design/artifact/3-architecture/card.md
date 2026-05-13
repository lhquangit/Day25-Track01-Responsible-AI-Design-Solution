---
artifact: 3 — Lớp kiến trúc dữ liệu
bai-tap: 2 — Thiết kế giải pháp
demo: ./demo.md
---

# card.md — Lớp kiến trúc dữ liệu

**Tình huống xử lý**: T-U-03 / U-03  
Xem `../../1-map-and-format.md` Phần A.

---

## 1. Giải pháp là gì?

Kiến trúc dữ liệu phải buộc mọi insight cuối tháng đi qua một lớp kiểm tra trạng thái dữ liệu trước khi cho AI trả lời. Cụ thể, hệ thống cần tách rõ `giao dịch đã xác nhận`, `giao dịch chưa xác nhận`, và `nguồn còn thiếu` như tiền mặt / ví điện tử / giao dịch chưa import; nếu thiếu dữ liệu quan trọng hoặc còn quá nhiều dòng mơ hồ, AI không được suy diễn tiếp mà phải trả về trạng thái `chưa thể kết luận` kèm bước rà soát hoặc chuyển người thật.  

Ngoài đường xử lý chính, hệ thống cũng phải ghi log các case `thiếu nguồn`, `refuse`, `OCR mờ`, `giao dịch cần xác nhận`, để nhóm biết lỗi nào đang lặp lại nhiều và nên ưu tiên sửa ở bản sau.

Ví dụ:

> Với câu hỏi về học bổng, hệ thống phải tra nguồn tuyển sinh chính thức trước khi AI trả lời. Nếu nguồn không có dữ liệu hoặc bị lỗi, AI không được đoán mà chuyển câu hỏi cho tư vấn viên.

---

## 2. Vì sao sửa ở lớp kiến trúc dữ liệu?

[x] Nguyên nhân chính là thiếu nguồn đúng hoặc nguồn cũ.
[x] AI đang phải tự nhớ thông tin thay vì đọc từ nguồn đáng tin cậy.
[x] Cần kiểm tra dữ liệu trước khi câu trả lời được tạo ra.
[x] Cần ghi lại lỗi để nhóm biết lỗi nào lặp lại nhiều.

**Hành động phòng vệ chính**:

- [x] Ngăn lỗi bằng nguồn dữ liệu đúng
- [x] Phát hiện khi nguồn thiếu hoặc lỗi
- [x] Khắc phục bằng cách chuyển sang người thật
- [x] Ghi lại lỗi để cải thiện sau

---

## 3. Demo nằm ở đâu?

**File demo**: [`demo.md`](./demo.md)

Demo cần có:

- Sơ đồ cách dữ liệu đi qua hệ thống
- Nguồn dữ liệu chính thức
- Bước kiểm tra trước khi AI trả lời
- Cách xử lý khi nguồn thiếu, lỗi hoặc quá cũ
- Cách ghi lại hoặc theo dõi lỗi

**Hướng demo nhóm nên thể hiện**

- Entry point: user hỏi ở màn hình báo cáo tháng.
- Bộ phân loại / router: nhận diện câu hỏi có liên quan đến quyết định cắt giảm hoặc cần độ chắc cao.
- Nguồn dữ liệu chính: transaction store đã xác nhận của user.
- Nguồn phụ: giao dịch import nhưng chưa xác nhận, OCR result, và trạng thái “thiếu nguồn” như chưa có ví điện tử / tiền mặt.
- Fallback: nếu thiếu dữ liệu hoặc confidence thấp thì không cho LLM chốt insight, mà trả về `refuse + review flow`.
- Logging / monitoring: đếm số lần `no-data`, `need-confirmation`, `refuse`, `handoff`.

---

## 4. Tác dụng phụ

**Có thể gây vấn đề gì?**

- Trả lời có thể chậm hơn vì phải kiểm tra trạng thái dữ liệu trước khi gọi model hoặc trước khi render insight.
- Hệ thống phức tạp hơn vì phải quản lý nhiều nguồn: dữ liệu đã xác nhận, dữ liệu OCR, dữ liệu chưa nhập, và metadata confidence.
- Nếu rule quá chặt, AI có thể từ chối nhiều hơn mức cần thiết, làm user cảm thấy app “kém thông minh”.
- Nhóm sẽ phải duy trì logging và taxonomy trạng thái dữ liệu; nếu làm nửa vời thì khó đo được lỗi thật đang nằm ở đâu.

**Nhóm giảm vấn đề đó bằng cách nào?**

- Chỉ áp dụng đường kiểm tra chặt với các câu hỏi rủi ro cao như `nên cắt khoản nào`, `tiền đi đâu`, `có giao dịch lạ không`, thay vì bật full guardrail cho mọi prompt nhỏ.
- Cache các summary an toàn trên dữ liệu đã xác nhận để giảm latency cho các câu hỏi lặp lại.
- Tách rule đơn giản và dễ kiểm thử: nếu còn thiếu nguồn quan trọng hoặc còn quá nhiều dòng chưa xác nhận thì không cho model kết luận.
- Gắn metric theo reason code (`missing_cash`, `missing_ewallet`, `ocr_low_confidence`, `need_user_confirmation`) để tuning dần thay vì sửa mù.

---

## 5. Checklist trước khi nộp

- [ ] Sơ đồ cho thấy dữ liệu đi từ đâu đến đâu.
- [x] Có bước kiểm tra nguồn trước khi AI trả lời.
- [x] Có cách xử lý khi không có dữ liệu.
- [x] Có cách chuyển sang người thật với tình huống rủi ro cao.
- [x] Có cách biết lỗi này có đang lặp lại không.

**Người phụ trách**: Quan
