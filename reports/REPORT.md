# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** NGUYỄN XUÂN SƠN<br>
**MSSV:** 2A202602155<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh: drive_008, drive_022, drive_033, drive_038
- Số vật thể thực tế: 86
- Mã SHA-256 của gói YOLO của bạn: aabb520add4c2aac58d5564af1e46a7aed63ed0572654d0cc834f8bfeae2321e
- Mã SHA-256 của gói CVAT gốc của bạn: ca1b1457d567de5d513a21854df7066d188eb8d2944ab550f41d5865d8e2a44d
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp: teaching_reference
- Mã SHA-256 của gói đối chiếu: c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu:
  11:46AM

- Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:
  Tôi tự gán nhãn cả bốn ảnh và tự kiểm tra bài của mình trước khi xem bài của người khác hoặc bộ nhãn tham chiếu. Sau khi hoàn thành và lưu bản xuất độc lập, tôi mới sử dụng bộ nhãn tham chiếu do Lab Coach cung cấp để đối chiếu.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_008.jpg/ CAR 38 | car | Nhìn thấy thân xe và hình dáng tổng thể đủ để nhận diện là ô tô con | Chỉ phân lớp dựa trên bằng chứng nhìn thấy, không đoán phần bị che |
| drive_008.jpg/ BUS 3 | bus | Vật thể có kích thước lớn, thân xe dài và cao, có nhiều cửa sổ/cửa lên xuống đặc trưng của xe buýt; hình dáng tổng thể phù hợp với xe buýt. | Phân lớp dựa trên các đặc điểm nhìn thấy của phương tiện; không suy đoán phần bị che khuất. |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:
Ví dụ:
  BUS 3 có lớp = bus, còn các thuộc tính là visibility = clear, boundary = inside, review_state = confident. Lớp cho biết loại phương tiện, trong khi thuộc tính mô tả mức độ quan sát, vị trí so với mép ảnh và trạng thái xem lại của vật thể.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Một số hộp chưa phản ánh đúng mức độ nhìn thấy/biên ảnh, đặc biệt trường hợp chỉ thấy một phần nhỏ như CAR 61: visibility=unclear, boundary=truncated, review_state=confident. | phạm vi/thuộc tính | Kiểm tra lại trực tiếp trên ảnh gốc và đối chiếu với quy tắc visibility + boundary | CAR 61: visibility=unclear, boundary=truncated, review_state=needs_review. Quy tắc: chỉ gán confident khi bằng chứng hình ảnh đủ rõ; không đoán phần bị che/khuất khỏi ảnh|

- Số hộp `needs_review` trước và sau khi kiểm:
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ:
    CAR 73 — chỉ nhìn thấy một phần nhỏ đầu của xe và phần lớn vật thể bị giới hạn bởi mép ảnh, nên chưa đủ bằng chứng để chắc chắn về đặc điểm của toàn bộ xe.
  + Xin hỗ trợ:
    Tôi đặt review_state = needs_review, ghi rõ lý do trong nhật ký quyết định và hỏi Lab Coach xác nhận cách phân lớp/thuộc tính đối với trường hợp này.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: lớp=2 (bus) | tâm=(0.3852, 0.7308) | kích thước=(0.4530, 0.3857)
- Tên lớp và tọa độ điểm ảnh `xyxy`: pixel xyxy: [101.6, 344.3, 391.5, 591.1]
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Một dòng YOLO có thể đúng cú pháp và có đủ năm giá trị nhưng vẫn sai nội dung. class có thể gán nhầm loại xe, còn các tọa độ có thể tạo ra hộp quá rộng, quá hẹp hoặc không bao sát phần phương tiện nhìn thấy. Vì vậy cần kiểm tra cả lớp, phạm vi và hình học chứ không chỉ kiểm tra định dạng dòng.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: drive_022, drive_033, drive_038
- Mã ảnh thẩm định: drive_008
- Mô tả một dự đoán trong `detect_result.jpg`: 
  Mô hình phát hiện chiếc ô tô màu trắng ở gần phía dưới ảnh và tạo bounding box bao quanh phần lớn thân xe. Bounding box dự đoán khá gần với hộp nhãn tham chiếu nhưng có sai lệch nhẹ về vị trí và kích thước.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào?
  Dự đoán cho thấy cần kiểm lại độ sát của bounding box, đặc biệt với các xe có kích thước nhỏ hoặc nằm xa camera. Cần bảo đảm hộp nhãn chỉ bao quanh phần phương tiện nhìn thấy và không mở rộng quá mức.
- Minh chứng nào có thể bác bỏ nhận định của bạn?
  Ảnh gốc và kết quả đối chiếu bounding box giữa nhãn thủ công và dự đoán của mô hình có thể cho thấy sai lệch là do mô hình dự đoán hay do cách gán nhãn.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?
  Vì chỉ có 4 ảnh, trong đó chỉ 3 ảnh dùng để huấn luyện và 1 ảnh để thẩm định, thời gian huấn luyện chỉ 8 epochs. Bộ dữ liệu quá nhỏ và không đủ đại diện cho các tình huống thực tế, nên kết quả chỉ dùng để phản hồi và tìm lỗi dữ liệu, không thể dùng để kết luận mô hình hoạt động tốt trong thực tế.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 48
- IoU trung bình và trung vị: 0.808236 và 0.816635
- Mức đồng thuận lớp: 72.9167% (≈ 72.92%)
- Số hộp phía bạn không ghép được:38
- Số hộp phía đối chiếu không ghép được: 2

- Một điểm khác biệt cụ thể:
  + Số vật thể được tôi đóng hộp là 86 và số hộp đối chiếu là 50.
  Có sự khác biệt về phân lớp giữa một số hộp dù các hộp vẫn được ghép theo hình học. Mức đồng thuận lớp chỉ đạt khoảng 72.92%, cho thấy một số vật thể cần được kiểm tra lại quyết định phân lớp.

- Quy tắc hoặc hành động sửa phát sinh:
  Kiểm tra lại các trường hợp có cùng vị trí/hộp nhưng khác lớp, đối chiếu với ảnh gốc và quy tắc phân lớp car, truck, bus, van. Với trường hợp chưa đủ bằng chứng, đánh dấu review_state = needs_review và ghi lại lý do.

- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?
  Vì hai bộ nhãn có thể cùng đưa ra một quyết định sai. Mức đồng thuận chỉ cho biết hai bên giống nhau ở mức nào, không chứng minh quyết định đó đúng với ảnh gốc và quy tắc. Vì vậy cần kiểm tra ảnh, quy tắc phân lớp và các trường hợp khác biệt.


## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

  - Bộ nhãn độc lập của tôi trên 4 ảnh, kết quả kiểm tra hai gói xuất YOLO/CVAT và kết quả đối chiếu cho thấy 48 hộp ghép được với IoU trung bình 0.808236, trung vị 0.816635 và mức đồng thuận lớp 72.9167%. Các trường hợp mơ hồ như BUS 3 và CAR 61 cũng có quyết định và thuộc tính được ghi rõ.
  - Câu hỏi cho Lab Coach: Với các trường hợp phương tiện chỉ nhìn thấy một phần nhỏ hoặc bị che khuất, tiêu chí nào là đủ để giữ class và đánh dấu confident, khi nào cần chuyển sang needs_review?
