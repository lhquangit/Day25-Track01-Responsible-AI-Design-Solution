---
artifact: 1 — Demo giao diện
format: phác thảo / ảnh / HTML / các màn hình chính
---

# demo.md — Demo giao diện

Demo này minh hoạ cách UI chặn rủi ro `U-03`: user muốn chốt ngân sách hoặc cắt giảm chi tiêu trong khi dữ liệu tháng còn thiếu hoặc chưa xác nhận.

---

## 1. State 1 — DEFAULT: Có đủ dữ liệu để tóm tắt có điều kiện

```text
╔════════════ Báo cáo tháng ════════════╗
║ User: "Tháng này tôi nên xem lại      ║
║ khoản nào trước?"                     ║
║                                       ║
║ AI tóm tắt                            ║
║ Ăn uống đang cao nhất: 4.800.000đ     ║
║ Đi lại: 1.250.000đ                    ║
║                                       ║
║ [✓ Đã xác nhận 42 giao dịch]          ║
║ [i Dựa trên dữ liệu đã ghi nhận]      ║
║                                       ║
║ [Mở giao dịch gốc] [Xem chi tiết]     ║
║                     [Xem với người thật]║
╚═══════════════════════════════════════╝
```

**Annotations**:
- Badge `Đã xác nhận 42 giao dịch` là thành phần quan trọng nhất vì nó nói rõ insight này dựa trên dữ liệu nào.
- `Mở giao dịch gốc` là hành động chính; `Xem với người thật` vẫn có nhưng là secondary.
- Nếu user bỏ qua badge, safeguard còn lại là AI wording vẫn chỉ nói `đang cao nhất trong dữ liệu đã ghi nhận`, không phải kết luận tuyệt đối.

## 2. State 2 — UNCERTAIN: Còn giao dịch chưa xác nhận

```text
╔════════════ Báo cáo tháng ════════════╗
║ User: "Vậy tôi nên cắt ăn uống đúng   ║
║ không?"                               ║
║                                       ║
║ AI tóm tắt                            ║
║ Trong các giao dịch đã ghi nhận,      ║
║ Ăn uống đang cao nhất.                ║
║                                       ║
║ [! Còn 6 giao dịch chưa phân loại]    ║
║ [! Có 3 dòng cần bạn xác nhận]        ║
║                                       ║
║ [Kiểm tra lại giao dịch]              ║
║ [Xác nhận từng khoản]                 ║
║ [Tạm chưa quyết định cắt giảm]        ║
╚═══════════════════════════════════════╝
```

**Annotations**:
- Hai badge cảnh báo biến uncertainty thành thứ user nhìn thấy được, thay vì giấu trong một câu disclaimer nhỏ.
- `Kiểm tra lại giao dịch` phải là action nổi bật nhất để kéo user về bước an toàn hơn.
- Nếu user vẫn cố bỏ qua cảnh báo, safeguard còn lại là UI không hiển thị recommendation kiểu `cắt 50%` ở state này.

## 3. State 3 — NO DATA / REFUSE: Thiếu nguồn dữ liệu quan trọng

```text
╔════════════ Báo cáo tháng ════════════╗
║ User: "Cứ nói thẳng đi, tôi nên cắt   ║
║ khoản nào ngay tối nay?"              ║
║                                       ║
║ AI chưa thể kết luận khoản nên cắt.   ║
║ Tháng này app mới có giao dịch ngân   ║
║ hàng, chưa có tiền mặt và ví điện tử. ║
║                                       ║
║ [! Dữ liệu tháng này chưa đầy đủ]     ║
║ [→ Cần nhập thêm trước khi quyết định]║
║                                       ║
║ [Nhập thêm dữ liệu thiếu]             ║
║ [Rà soát giao dịch chưa rõ]           ║
║ [Xem với người thật]                  ║
╚═══════════════════════════════════════╝
```

**Annotations**:
- Câu `AI chưa thể kết luận` là điểm quan trọng nhất vì nó chặn thói quen dùng AI như cố vấn tài chính cuối cùng.
- `Nhập thêm dữ liệu thiếu` là action chính; state này phải dẫn user về dữ liệu trước, không dẫn tới quyết định.
- Nếu user vẫn bỏ qua state này, safeguard còn lại là không có nút xác nhận một recommendation cụ thể nào để họ click tiếp.

## 4. State 4 — Chuyển sang người thật / hỗ trợ kiểm tra

```text
╔════════════ Cần kiểm tra thêm ════════╗
║ User: "Ảnh sao kê này mờ nhưng tôi    ║
║ đang chuẩn bị khóa thẻ, xem nhanh giúp║
║ tôi có khoản nào lạ không."           ║
║                                       ║
║ AI không nên tự kết luận từ ảnh mờ.   ║
║ Có giao dịch chưa đọc chắc chắn.      ║
║                                       ║
║ [! Cần người thật kiểm tra cùng]      ║
║ [! Tránh khóa thẻ dựa trên dữ liệu mờ]║
║                                       ║
║ [Gọi hỗ trợ viên] [Tải ảnh rõ hơn]    ║
║ [Xem lại giao dịch gốc]               ║
╚═══════════════════════════════════════╝
```

**Annotations**:
- Cụm `Cần người thật kiểm tra cùng` là thành phần quan trọng nhất vì state này chuyển từ AI-support sang AI-stop.
- `Gọi hỗ trợ viên` cần nổi bật nhất khi harm có thể xảy ra trong vài phút.
- Nếu user không chọn handoff, safeguard còn lại là UI không hiển thị kết luận `không có gì lạ` từ dữ liệu mờ.

---

## 5. Trạng thái cần minh họa

| Trạng thái | Người dùng thấy gì? | Người dùng làm gì tiếp? |
|---|---|---|
| Có nguồn xác minh | Insight có badge `Đã xác nhận`, có câu nhắc “dựa trên dữ liệu đã ghi nhận” | Mở giao dịch gốc hoặc xem chi tiết |
| Chưa có nguồn xác minh | Cảnh báo còn giao dịch chưa phân loại / chưa xác nhận | Kiểm tra lại giao dịch hoặc xác nhận từng khoản |
| AI không nên tự trả lời | Refuse rõ vì thiếu tiền mặt / ví điện tử / dữ liệu chưa nhập | Nhập thêm dữ liệu thiếu trước khi quyết định |
| Cần chuyển sang người thật | Cảnh báo không nên tự kết luận từ ảnh mờ hoặc dữ liệu rủi ro cao | Gọi hỗ trợ viên hoặc xem lại giao dịch gốc |

---

## 6. Ghi chú cho từng thành phần

- **Badge trạng thái**: đặt ngay dưới phần trả lời AI; dùng text + ký hiệu như `✓`, `!`, `→` để không phụ thuộc vào màu.
- **Khối “dữ liệu còn thiếu”**: nằm giữa phần trả lời và action buttons; chỉ hiện khi có giao dịch chưa xác nhận hoặc chưa nhập đủ nguồn.
- **Action chính**: ở state an toàn là `Mở giao dịch gốc`; ở state rủi ro là `Kiểm tra lại giao dịch`, `Nhập thêm dữ liệu thiếu`, hoặc `Gọi hỗ trợ viên`.
- **Action chuyển người thật**: luôn tồn tại, nhưng chỉ trở nên prominent ở state 3 và state 4.
- **Wording an toàn**: tránh câu như `bạn nên cắt ngay`; thay bằng `trong dữ liệu đã ghi nhận`, `chưa thể kết luận`, `cần kiểm tra thêm`.

---

## 7. User testing plan

| Scenario | User profile | Task | Pass criteria |
|---|---|---|---|
| A — User vội | Người mới đi làm, đang chốt ngân sách buổi tối | Hỏi “tôi nên cắt khoản nào ngay?” khi thiếu ví điện tử | ≥ 70% nhận ra state `chưa thể kết luận` và chọn `Nhập thêm dữ liệu thiếu` hoặc `Kiểm tra lại giao dịch` |
| B — Pressure-trap | User muốn AI “ước chừng cũng được” | Đọc state uncertain sau khi còn giao dịch chưa phân loại | ≥ 60% không ra quyết định ngay; họ quay lại bước xác nhận giao dịch |
| C — OCR rủi ro cao | User nghi có giao dịch lạ và định khóa thẻ | Nhìn state ảnh mờ / cần người thật kiểm tra | ≥ 70% chọn `Gọi hỗ trợ viên` hoặc `Xem lại giao dịch gốc` trước khi hành động |

## 8. Câu hỏi cần verify với user thật

- User có thực sự nhìn thấy và hiểu khác biệt giữa `Đã xác nhận`, `Chưa chắc`, và `Chưa thể kết luận` không?
- Khi đang vội, user có click `Kiểm tra lại giao dịch` hay vẫn cố tìm một kết luận ngắn để quyết định ngay?
- Cụm `Xem với người thật` có đủ rõ không, hay cần wording cụ thể hơn như `Nhờ hỗ trợ viên kiểm tra`?

---

## 9. Kiểm tra nhanh

- [x] Nhìn vào demo là hiểu rủi ro đang được chặn ở đâu.
- [x] Có trạng thái khi AI không có đủ thông tin.
- [x] Có cách chuyển sang người thật.
- [x] Câu chữ đủ ngắn để đặt trên màn hình thật.
