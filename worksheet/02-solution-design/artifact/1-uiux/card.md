---
artifact: 1 — Lớp giao diện
bai-tap: 2 — Thiết kế giải pháp
demo: ./demo.md
---

# card.md — Lớp giao diện

**Tình huống xử lý**: T-U-03 / U-03  
Xem `../../1-map-and-format.md` Phần A.

---

## 1. Giải pháp là gì?

Khi user hỏi nên cắt khoản nào hoặc chốt ngân sách trong lúc dữ liệu còn thiếu, giao diện không cho câu trả lời AI xuất hiện như một kết luận hoàn chỉnh. Màn hình sẽ tách rõ `đã xác nhận`, `chưa xác nhận`, `có thể còn thiếu`, gắn nhãn mức chắc chắn cho insight, và đưa các hành động `Kiểm tra lại giao dịch`, `Xác nhận các khoản chưa rõ`, hoặc `Nhập thêm dữ liệu thiếu` lên vị trí nổi bật khi AI chưa đủ cơ sở.  

Mục tiêu của lớp giao diện là làm cho user nhìn thấy ngay: đây là gợi ý có điều kiện từ dữ liệu hiện có, không phải “phán quyết tài chính” cuối cùng.

Ví dụ:

> Khi user hỏi “Tôi nên cắt khoản nào ngay?”, giao diện không chỉ hiện câu trả lời “Ăn uống đang cao nhất” mà còn kèm nhãn như “Dựa trên giao dịch đã ghi nhận”, “Còn 6 giao dịch chưa phân loại”, và nút `Rà soát trước khi quyết định`. Nếu app chưa có tiền mặt hoặc ví điện tử, giao diện phải hiện rõ “Dữ liệu tháng này có thể chưa đầy đủ”.

---

## 2. Vì sao sửa ở lớp giao diện?

[x] Người dùng dễ tin câu trả lời của AI quá mức.
[x] Rủi ro xảy ra ở khoảnh khắc người dùng đọc câu trả lời.
[x] Giao diện cần làm rõ: dữ liệu nào đã xác nhận, dữ liệu nào còn mơ hồ, và phần nào có thể còn thiếu.

- [x] Nếu prompt hoặc dữ liệu vẫn sót lỗi, giao diện là lớp chặn cuối.

**Hành động phòng vệ chính**:

- [x] Thông báo rõ giới hạn của insight
- [x] Phát hiện dữ liệu còn thiếu hoặc giao dịch chưa xác nhận
- [x] Ngăn insight bị hiểu như kết luận cuối cùng
- [x] Dẫn người dùng về bước rà soát phù hợp trước khi quyết định

---

## 3. Demo nằm ở đâu?

**File demo**: [`demo.md`](./demo.md)

**Định dạng demo**:

- [x] Phác thảo màn hình
- [x] Luồng màn hình
- [ ] Bản HTML đơn giản
- [ ] Ảnh hoặc link prototype

**Thành phần cần có trong demo**:

- Trạng thái dữ liệu đủ để AI tóm tắt có điều kiện
- Trạng thái còn giao dịch chưa xác nhận hoặc chưa phân loại
- Trạng thái còn thiếu nguồn dữ liệu như tiền mặt / ví điện tử / giao dịch chưa import
- Cách người dùng đi tiếp sang `Kiểm tra lại giao dịch` hoặc `Xác nhận từng khoản`
- Câu chữ cảnh báo ngắn, dễ hiểu, không phán xét user

---

## 4. Tác dụng phụ

**Có thể gây vấn đề gì?**

- Màn hình có thể rối hơn nếu gắn quá nhiều badge, trạng thái và cảnh báo cùng lúc.
- User đang vội có thể thấy bị chậm nhịp vì phải xác nhận thêm thay vì nhận ngay một kết luận ngắn.
- Nếu wording quá nặng tay, user có thể hiểu nhầm rằng app “không giúp được gì”, dù thật ra app đang cố giữ an toàn.

**Nhóm giảm vấn đề đó bằng cách nào?**

- Chỉ làm nổi bật cảnh báo khi câu trả lời liên quan đến quyết định cắt giảm, dữ liệu thiếu, hoặc giao dịch chưa xác nhận.
- Dùng nhãn ngắn và thuần Việt như `Đã xác nhận`, `Chưa chắc`, `Cần kiểm tra thêm` thay cho disclaimer dài.
- Đưa chi tiết kỹ thuật vào vùng mở rộng; phần chính của màn hình chỉ giữ 1-2 hành động quan trọng nhất như `Kiểm tra lại giao dịch` và `Nhập thêm dữ liệu thiếu`.

**Giới hạn của lớp giao diện**

- Lớp UI không tự sửa được việc model suy diễn quá mức; nó chỉ giảm nguy cơ user hiểu nhầm và dẫn user về bước an toàn hơn.
- Vì vậy giải pháp này cần đi cùng prompt layer để ép AI nói theo mức chắc chắn, và architecture layer để biết giao dịch nào đã xác nhận / chưa xác nhận / còn thiếu.

---

## 5. Checklist trước khi nộp

- [x] Giải pháp gắn đúng với một rủi ro chính.
- [x] Card nói rõ risk chính là over-reliance do dữ liệu thiếu hoặc chưa xác nhận, không phải bài toán source verification chung chung.
- [ ] Demo nhìn vào là hiểu vấn đề được chặn ở đâu.
- [ ] Có đủ trạng thái bình thường và trạng thái lỗi.
- [x] Có hành động tiếp theo khả thi khi AI chưa nên đưa kết luận chắc chắn.
- [x] Câu chữ trong giao diện ngắn, không đổ hết trách nhiệm cho người dùng.

**Người phụ trách**: Quan
