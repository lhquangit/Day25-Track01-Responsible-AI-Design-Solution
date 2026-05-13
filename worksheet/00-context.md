---

# 00-context.md — Bối cảnh sản phẩm của nhóm

---

## 1. Sản phẩm

- **Tên sản phẩm / bot**: Trợ lý ghi chú và tổng hợp chi tiêu
- **Sản phẩm giúp ai làm gì**: Giúp người dùng cá nhân ghi lại chi tiêu bằng text/voice, import giao dịch từ sao kê hoặc ví điện tử, gợi ý category, và tóm tắt chi tiêu cuối tháng để họ hiểu tiền đang đi vào đâu.
- **Người dùng gặp sản phẩm ở đâu**: Ứng dụng mobile, đặc biệt ở màn hình thêm khoản chi, màn hình import giao dịch, và tab báo cáo tháng có chatbot hỏi-đáp.
- **Giai đoạn hiện tại**: Đang thử nghiệm / mô phỏng use case trong bài tập thiết kế sản phẩm AI.
- **Job-to-be-done chính**: Giúp user nhìn lại chi tiêu cuối tháng nhanh hơn mà không phải nhập tay hoặc tự tổng hợp toàn bộ, nhưng vẫn giữ quyền quyết định cuối cùng ở user.
- **Workflow lõi của sản phẩm**: `Capture` khoản chi bằng text/voice -> `Import` giao dịch từ ảnh chụp sao kê / ví điện tử -> `Categorize` và tạo bản nháp -> `Confirm` để user duyệt các dòng chưa chắc chắn -> `Summarize` ở báo cáo tháng bằng insight có điều kiện.
- **Nguyên tắc sản phẩm cốt lõi**: AI là lớp hỗ trợ hiểu dữ liệu chi tiêu, không phải “financial advisor” tự ra kết luận hay tự thay user quyết định ngân sách.

---

## 2. Phạm vi

**AI được làm gì**

- Nhận khoản chi bằng câu tự nhiên hoặc giọng nói, tách thành từng giao dịch, rồi tạo bản nháp để user xác nhận trước khi lưu.
- Gợi ý category cho giao dịch và hỏi lại khi thiếu số tiền, thời gian, merchant hoặc mô tả.
- Đọc giao dịch từ ảnh chụp sao kê / lịch sử ngân hàng, trích xuất thông tin cơ bản, và đánh dấu các dòng mơ hồ là “cần xác nhận”.
- Tóm tắt báo cáo cuối tháng dựa trên dữ liệu hiện có, nhưng phải nói rõ phạm vi dữ liệu đang dùng và những giao dịch còn thiếu / chưa phân loại.
- Hiển thị insight theo mức độ chắc chắn khác nhau: giao dịch đã xác nhận, giao dịch chưa phân loại, dữ liệu có thể còn thiếu như tiền mặt / ví điện tử / khoản chưa import.

**AI không được làm gì**

- Không được tự ý sửa số tiền, tự xoá giao dịch, hoặc tự đổi category mà user đã xác nhận.
- Không được đoán merchant hoặc category khi ảnh mờ, tên giao dịch viết tắt, hoặc dữ liệu mơ hồ như `QR PAY`, `CK`, `POS MCH`.
- Không được đưa kết luận chắc chắn hoặc lời khuyên tài chính quá cụ thể như “bạn nên cắt 50% khoản này” khi dữ liệu còn thiếu, chưa xác nhận, hoặc chưa phân loại xong.
- Không được hiển thị hoặc nhắc lại quá chi tiết các thông tin nhạy cảm trong báo cáo / notification như bệnh viện, nhà thuốc, địa điểm riêng tư, hoặc chuyển khoản cá nhân nếu không thật sự cần thiết.
- Không được biến dashboard hoặc chatbot thành nơi “phán quyết” ai tiêu sai trong bối cảnh chi tiêu gia đình / cặp đôi khi dữ liệu còn mơ hồ.

**Vì sao có giới hạn này**

Vì dữ liệu chi tiêu cá nhân rất nhạy cảm, thường không đầy đủ và dễ mơ hồ. Nếu AI tự suy diễn quá mức, user có thể hiểu sai tình hình tài chính, cắt giảm sai khoản cần thiết, tranh cãi với người thân, hoặc bị lộ thông tin riêng tư về sức khỏe, vị trí và thói quen sống.

**Ranh giới hệ thống nên được xem là mặc định**

- Dữ liệu ưu tiên theo thứ tự: giao dịch user đã xác nhận -> giao dịch import nhưng chưa xác nhận -> dữ liệu user nói còn thiếu nhưng chưa nhập.
- Mọi insight cuối tháng phải bám vào dữ liệu hiện có, không được giả định app đã nhìn thấy “toàn bộ bức tranh chi tiêu”.
- Nếu còn giao dịch mơ hồ hoặc nguồn dữ liệu chưa đủ, trạng thái đúng của hệ thống là “cần user xác nhận”, không phải “tự hoàn tất cho mượt”.

---

## 3. Người dùng

- **Là ai**: Người dùng cá nhân Việt Nam khoảng 22–35 tuổi; nhiều người mới đi làm 0–3 năm, có lương cố định, chưa quen budgeting; một phần là người đang quản lý ngân sách gia đình nhỏ.
- **Họ hỏi AI khi nào**: Khi nhập lại chi tiêu vào buổi tối hoặc cuối tuần; khi import ảnh chụp giao dịch cuối tháng; khi mở tab báo cáo tháng và hỏi kiểu “tháng này tiền của tôi đi đâu?” hoặc “tôi nên cắt khoản nào?”.
- **Họ cần quyết định gì sau khi hỏi AI**: Có nên cắt giảm nhóm chi nào, ngân sách tháng sau nên điều chỉnh ra sao, giao dịch nào cần rà soát lại, và category nào cần xác nhận thủ công.
- **Khi nào họ dễ bị tổn thương / dễ hiểu sai**: Khi dữ liệu chưa có tiền mặt hoặc ví điện tử; còn nhiều giao dịch chưa phân loại; ảnh sao kê bị mờ hoặc tên merchant quá mơ hồ; user đang lo lắng vì “tháng này nghèo quá”; hoặc khi báo cáo AI được hiển thị cạnh biểu đồ khiến nó trông rất đáng tin.
- **Họ thường tin AI đến mức nào**: Khá dễ tin và thường chấp nhận category AI gợi ý mà không kiểm tra từng dòng, nhất là khi đang nhập nhiều khoản nhỏ hoặc xem insight ở cuối tháng.
- **Phân khúc chính**: Người mới đi làm, nhập liệu muộn sau khi đã tiêu xong, muốn biết nhóm chi lớn nhất để tự điều chỉnh tháng sau.
- **Phân khúc phụ**: Người đã lập gia đình hoặc đang quản lý ngân sách hộ gia đình nhỏ, nơi một insight sai có thể kéo theo hiểu nhầm giữa các thành viên.
- **Hành vi quan trọng cần nhớ**: User thường dùng ngôn ngữ đời thường và cảm tính hơn là câu hỏi “chuẩn dữ liệu”, ví dụ “sao tháng này tôi nghèo thế?” hoặc “có phải tôi ăn uống hoang quá không?”.

---

## 4. Bối cảnh ngành

- **Các pattern failure đang define dự án**:
  - `Hallucination / factual error`: Đoán merchant hoặc category từ ảnh sao kê mờ / tên viết tắt.
  - `Privacy / data leak`: Đưa quá nhiều chi tiết nhạy cảm vào insight hoặc notification.
  - `Over-reliance`: Biến dữ liệu thiếu thành kết luận chắc chắn hoặc lời khuyên cắt giảm chi tiêu.
- **Primary failure được ưu tiên xử lý**: Over-reliance ở màn hình báo cáo tháng, vì đây là điểm user dễ tin AI nhất và dễ ra quyết định tài chính sai nhất.
- **Quy định hoặc ràng buộc liên quan**: Report chưa nêu văn bản pháp lý cụ thể, nhưng sản phẩm phải coi dữ liệu chi tiêu cá nhân là dữ liệu nhạy cảm; chỉ nên kết luận trong phạm vi dữ liệu đã ghi nhận; phải cảnh báo rõ khi dữ liệu thiếu; và phải tối thiểu hoá việc hiển thị thông tin riêng tư trong insight hoặc notification.
- **Nguồn chính thức nên ưu tiên**: Giao dịch user đã lưu và xác nhận; dữ liệu sao kê / ngân hàng / ví điện tử do chính user cung cấp; category taxonomy nội bộ của sản phẩm; và phần nào chưa chắc thì phải hỏi lại user thay vì đoán.
- **Bối cảnh trải nghiệm cần nhớ**: Khi insight được đặt trong dashboard có biểu đồ và số liệu, mức độ thuyết phục của AI tăng mạnh; vì vậy UI copy và trạng thái cảnh báo là một phần của safety chứ không chỉ là phần model.

---

## 5. Ghi chú thêm

- Failure chính cần ưu tiên tránh: AI đưa kết luận chắc chắn hoặc lời khuyên cắt giảm chi tiêu khi dữ liệu còn thiếu, chưa phân loại, hoặc chưa được user xác nhận.
- Mức độ nghiêm trọng của failure chính: `High`, vì user có thể ra quyết định tài chính cá nhân sai hoặc áp đặt ngân sách sai lên người thân.
- Các câu hỏi thật dễ làm AI trượt nếu không cẩn thận: “Sao tháng này tôi nghèo thế?”, “Tiền tôi bay đi đâu hết rồi?”, “Có phải tôi ăn uống hoang quá không?”, “Cứ nói thẳng đi, tôi nên cắt 50% ăn uống đúng không?”.
- Nếu gặp giao dịch mơ hồ như `QR PAY`, `CK`, `POS MCH`, AI phải chuyển sang trạng thái “cần xác nhận” thay vì tự hoàn tất phân loại rồi tạo kết luận cuối tháng.
- Ở quy mô lớn, rủi ro không chỉ là câu trả lời sai một lần mà còn là tạo thói quen over-reliance: user dần ngừng kiểm tra giao dịch gốc và tin quá mức vào insight của AI.
- Hậu quả cần được nhìn như product harm thật, không chỉ là “AI trả lời chưa hay”: user có thể cắt nhầm khoản thiết yếu, bỏ qua khoản bất thường, trách nhầm người thân, hoặc rời bỏ app vì mất trust.
- Với dự án này, “an toàn” không có nghĩa là im lặng hoàn toàn; nghĩa là nói đúng mức chắc chắn, chỉ rõ phần còn thiếu, và dẫn user về bước xác nhận phù hợp.

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
