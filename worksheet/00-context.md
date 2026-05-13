---

# 00-context.md — Bối cảnh sản phẩm của nhóm

---

## 1. Sản phẩm

- **Tên sản phẩm / bot**: Trợ lý ghi chú và tổng hợp chi tiêu
- **Sản phẩm giúp ai làm gì**: Giúp người dùng cá nhân ghi lại chi tiêu bằng text/voice, import giao dịch từ sao kê hoặc ví điện tử, gợi ý category, và tóm tắt chi tiêu cuối tháng để họ hiểu tiền đang đi vào đâu.
- **Người dùng gặp sản phẩm ở đâu**: Ứng dụng mobile, đặc biệt ở màn hình thêm khoản chi, màn hình import giao dịch, và tab báo cáo tháng có chatbot hỏi-đáp.
- **Giai đoạn hiện tại**: Đang thử nghiệm / mô phỏng use case trong bài tập thiết kế sản phẩm AI.

---

## 2. Phạm vi

**AI được làm gì**

- Nhận khoản chi bằng câu tự nhiên hoặc giọng nói, tách thành từng giao dịch, rồi tạo bản nháp để user xác nhận trước khi lưu.
- Gợi ý category cho giao dịch và hỏi lại khi thiếu số tiền, thời gian, merchant hoặc mô tả.
- Đọc giao dịch từ ảnh chụp sao kê / lịch sử ngân hàng, trích xuất thông tin cơ bản, và đánh dấu các dòng mơ hồ là “cần xác nhận”.
- Tóm tắt báo cáo cuối tháng dựa trên dữ liệu hiện có, nhưng phải nói rõ phạm vi dữ liệu đang dùng và những giao dịch còn thiếu / chưa phân loại.

**AI không được làm gì**

- Không được tự ý sửa số tiền, tự xoá giao dịch, hoặc tự đổi category mà user đã xác nhận.
- Không được đoán merchant hoặc category khi ảnh mờ, tên giao dịch viết tắt, hoặc dữ liệu mơ hồ như `QR PAY`, `CK`, `POS MCH`.
- Không được đưa kết luận chắc chắn hoặc lời khuyên tài chính quá cụ thể như “bạn nên cắt 50% khoản này” khi dữ liệu còn thiếu, chưa xác nhận, hoặc chưa phân loại xong.
- Không được hiển thị hoặc nhắc lại quá chi tiết các thông tin nhạy cảm trong báo cáo / notification như bệnh viện, nhà thuốc, địa điểm riêng tư, hoặc chuyển khoản cá nhân nếu không thật sự cần thiết.

**Vì sao có giới hạn này**

Vì dữ liệu chi tiêu cá nhân rất nhạy cảm, thường không đầy đủ và dễ mơ hồ. Nếu AI tự suy diễn quá mức, user có thể hiểu sai tình hình tài chính, cắt giảm sai khoản cần thiết, tranh cãi với người thân, hoặc bị lộ thông tin riêng tư về sức khỏe, vị trí và thói quen sống.

---

## 3. Người dùng

- **Là ai**: Người dùng cá nhân Việt Nam khoảng 22–35 tuổi; nhiều người mới đi làm 0–3 năm, có lương cố định, chưa quen budgeting; một phần là người đang quản lý ngân sách gia đình nhỏ.
- **Họ hỏi AI khi nào**: Khi nhập lại chi tiêu vào buổi tối hoặc cuối tuần; khi import ảnh chụp giao dịch cuối tháng; khi mở tab báo cáo tháng và hỏi kiểu “tháng này tiền của tôi đi đâu?” hoặc “tôi nên cắt khoản nào?”.
- **Họ cần quyết định gì sau khi hỏi AI**: Có nên cắt giảm nhóm chi nào, ngân sách tháng sau nên điều chỉnh ra sao, giao dịch nào cần rà soát lại, và category nào cần xác nhận thủ công.
- **Khi nào họ dễ bị tổn thương / dễ hiểu sai**: Khi dữ liệu chưa có tiền mặt hoặc ví điện tử; còn nhiều giao dịch chưa phân loại; ảnh sao kê bị mờ hoặc tên merchant quá mơ hồ; user đang lo lắng vì “tháng này nghèo quá”; hoặc khi báo cáo AI được hiển thị cạnh biểu đồ khiến nó trông rất đáng tin.
- **Họ thường tin AI đến mức nào**: Khá dễ tin và thường chấp nhận category AI gợi ý mà không kiểm tra từng dòng, nhất là khi đang nhập nhiều khoản nhỏ hoặc xem insight ở cuối tháng.

---

## 4. Bối cảnh ngành

- **Sự cố tương tự đã từng xảy ra**: AI có thể phân loại sai giao dịch mơ hồ, đọc sai sao kê từ ảnh, đưa kết luận quá tự tin từ dữ liệu còn thiếu, hoặc làm lộ chi tiết nhạy cảm như bệnh viện, nhà thuốc, địa chỉ cửa hàng, chuyển khoản cho người thân.
- **Quy định hoặc ràng buộc liên quan**: Report chưa nêu văn bản pháp lý cụ thể, nhưng sản phẩm phải coi dữ liệu chi tiêu cá nhân là dữ liệu nhạy cảm; chỉ nên kết luận trong phạm vi dữ liệu đã ghi nhận; phải cảnh báo rõ khi dữ liệu thiếu; và phải tối thiểu hoá việc hiển thị thông tin riêng tư trong insight hoặc notification.
- **Nguồn chính thức nên ưu tiên**: Giao dịch user đã lưu và xác nhận; dữ liệu sao kê / ngân hàng / ví điện tử do chính user cung cấp; category taxonomy nội bộ của sản phẩm; và phần nào chưa chắc thì phải hỏi lại user thay vì đoán.

---

## 5. Ghi chú thêm

- Failure chính cần ưu tiên tránh: AI đưa kết luận chắc chắn hoặc lời khuyên cắt giảm chi tiêu khi dữ liệu còn thiếu, chưa phân loại, hoặc chưa được user xác nhận.
- Mức độ nghiêm trọng của failure chính: `High`, vì user có thể ra quyết định tài chính cá nhân sai hoặc áp đặt ngân sách sai lên người thân.
- Các câu hỏi thật dễ làm AI trượt nếu không cẩn thận: “Sao tháng này tôi nghèo thế?”, “Tiền tôi bay đi đâu hết rồi?”, “Có phải tôi ăn uống hoang quá không?”, “Cứ nói thẳng đi, tôi nên cắt 50% ăn uống đúng không?”.
- Nếu gặp giao dịch mơ hồ như `QR PAY`, `CK`, `POS MCH`, AI phải chuyển sang trạng thái “cần xác nhận” thay vì tự hoàn tất phân loại rồi tạo kết luận cuối tháng.
- Ở quy mô lớn, rủi ro không chỉ là câu trả lời sai một lần mà còn là tạo thói quen over-reliance: user dần ngừng kiểm tra giao dịch gốc và tin quá mức vào insight của AI.

---

## Cách dùng

```text
1. Mở công cụ AI phù hợp với bước đang làm.
2. Đưa toàn bộ nội dung file này vào đầu cuộc trò chuyện.
3. Chọn prompt tham khảo từ thư mục ../prompts/ và chỉnh lại nếu cần.
4. Đọc lại bản nháp AI tạo ra.
5. Sửa lại cho đúng bối cảnh nhóm.
6. Lưu kết quả vào đúng file trong worksheet/.
```

