---
artifact: 2 — Lớp chỉ dẫn AI
bai-tap: 2 — Thiết kế giải pháp
demo: ./demo.md
---

# card.md — Lớp chỉ dẫn AI

**Tình huống xử lý**: T2 / T3 / T4 / T5  
Xem `../../1-map-and-format.md` Phần A.

---

## 1. Giải pháp là gì?

Nhóm sẽ thêm một bộ luật prompt buộc AI trả lời theo `mức chắc chắn của dữ liệu` thay vì theo sức ép của câu hỏi. Nếu dữ liệu còn thiếu tiền mặt / ví điện tử, còn giao dịch chưa phân loại, hoặc có mã mơ hồ như `QR PAY`, `CK`, `POS MCH`, AI phải nói rõ giới hạn, chỉ được đưa insight có điều kiện, và dẫn user về bước `rà soát` hoặc `xác nhận thủ công` thay vì tự kết luận.

Prompt cũng phải chặn 3 hành vi nguy hiểm nhất của model trong bài toán này:

- Không xác nhận lời khuyên cắt giảm cụ thể như `cắt 50% ăn uống` nếu chưa đủ dữ liệu.
- Không đồng ý theo cảm xúc hoặc giả định của user như `đúng, bạn ăn uống hoang quá`.
- Không tự phân loại các giao dịch mơ hồ để tạo báo cáo cuối tháng.

Ví dụ:

> Khi user hỏi “Tôi nên cắt khoản nào ngay?”, AI chỉ được nói theo dạng: “Trong các giao dịch đã ghi nhận, Ăn uống đang cao nhất. Tuy nhiên mình chưa thể kết luận bạn nên cắt khoản nào ngay vì app còn thiếu dữ liệu tiền mặt / ví điện tử và còn giao dịch chưa xác nhận. Mình có thể giúp bạn rà soát các khoản chưa rõ trước.”

---

## 2. Vì sao sửa ở lớp chỉ dẫn AI?

- AI đang trả lời quá tự tin khi dữ liệu thiếu hoặc chưa xác nhận.
- AI đang dễ chiều theo giả định sai của người dùng, nhất là ở các câu hỏi kiểu cảm tính hoặc pressure trap.
- AI cần luật rõ: khi nào được tóm tắt có điều kiện, khi nào phải hỏi lại, khi nào phải từ chối kết luận, và khi nào phải chuyển user về bước xác nhận thủ công.
- Có thể sửa nhanh bằng prompt để chặn failure chính trước khi hệ thống dữ liệu và UI hoàn thiện đầy đủ.

**Hành động phòng vệ chính**:

- [x] Ngăn câu trả lời sai ngay từ đầu
- [x] Bắt buộc nêu phạm vi dữ liệu đang dùng
- [x] Từ chối trả lời khi thiếu căn cứ cho một kết luận chắc chắn
- [x] Chuyển user về bước rà soát / xác nhận phù hợp khi vượt ngưỡng an toàn

---

## 3. Demo nằm ở đâu?

**File demo**: [`demo.md`](./demo.md)

Demo cần có:

- Luật chính cho AI về `dữ liệu đã xác nhận`, `dữ liệu chưa xác nhận`, `dữ liệu có thể còn thiếu`
- Mẫu câu khi AI phải trả lời có điều kiện thay vì kết luận chắc chắn
- Mẫu câu khi AI cần từ chối xác nhận một lời khuyên quá cụ thể
- Mẫu câu khi AI gặp giao dịch mơ hồ và phải chuyển user sang bước xác nhận thủ công
- 2-3 ví dụ hỏi đáp bám trực tiếp các case T2, T3, T4, T5 trong Bài 1

---

## 4. Tác dụng phụ

**Có thể gây vấn đề gì?**

- AI có thể trở nên quá thận trọng và từ chối nhiều hơn mức cần thiết, làm user thấy “app trả lời vòng vo”.
- Nếu luật viết quá cứng, câu trả lời có thể nghe máy móc, lặp disclaimer nhiều lần, và làm giảm cảm giác hữu ích.
- Prompt một mình không sửa được gốc rễ nếu hệ thống không biết giao dịch nào đã xác nhận, chưa xác nhận, hay còn thiếu nguồn dữ liệu.

**Nhóm giảm vấn đề đó bằng cách nào?**

- Chỉ kích hoạt chế độ chặt hơn khi câu hỏi liên quan đến `cắt giảm`, `ra quyết định ngân sách`, hoặc khi hệ thống có tín hiệu dữ liệu thiếu / mơ hồ.
- Dùng mẫu câu ngắn, rõ, trung tính: nêu giới hạn dữ liệu trước, sau đó vẫn đưa insight có điều kiện và hành động tiếp theo cụ thể.
- Tách rõ 3 mức phản hồi:
  - `Được trả lời có điều kiện` khi dữ liệu đủ để tóm tắt tạm thời.
  - `Cần hỏi lại / cảnh báo` khi còn thiếu dữ liệu nhưng chưa đến mức phải dừng.
  - `Phải từ chối kết luận` khi user ép AI đưa ra lời khuyên cụ thể hoặc tự phân loại giao dịch mơ hồ.
- Phối hợp với lớp UI để hiển thị các trạng thái này rõ ràng, và với lớp architecture để cung cấp đúng tín hiệu `đã xác nhận / chưa xác nhận / thiếu dữ liệu`.

---

## 5. Checklist trước khi nộp

- [x] Luật viết đủ cụ thể để AI làm theo.
- [x] Có mẫu câu khi AI không có đủ thông tin.
- [x] Có ví dụ cho tình huống dễ sai.
- [ ] Có thử lại bằng tình huống trong Bài 1.
- [x] Không dùng prompt như cách duy nhất nếu lỗi nằm ở dữ liệu hoặc quy trình.

**Người phụ trách**: Quan
