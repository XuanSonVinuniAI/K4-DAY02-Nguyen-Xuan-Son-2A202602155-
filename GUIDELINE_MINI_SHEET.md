# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** NGUYỄN XUÂN SƠN<br>
**MSSV:** 2A202602155<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: drive_008 – BUS 3 (MANUAL)
- Dấu hiệu nhìn thấy: Vật thể có thân xe lớn, dài và cao, nhiều cửa sổ và hình dáng đặc trưng của xe buýt.
- Quy tắc áp dụng: Phân lớp dựa trên đặc điểm trực quan của phương tiện trong ảnh, không suy đoán những phần không quan sát được.
- Quyết định: bus
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đặt review_state = needs_review, ghi rõ phần bằng chứng chưa đủ và xin Lab Coach hỗ trợ.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: drive_008 – TRUCK 25 (MANUAL)
- Dấu hiệu nhìn thấy: Xe có cabin phía trước và thùng chở hàng lớn (đất, cát,...) phía sau, hình dáng rõ ràng của xe tải.
- Quy tắc áp dụng: Chọn truck khi có đặc điểm trực quan rõ ràng của cabin và thùng chở hàng; không dựa vào kích thước đơn thuần.
- Quyết định: truck
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đặt review_state = needs_review, ghi lại lý do và xin Lab Coach xác nhận thay vì tự đoán.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: drive_038 – CAR 73 (MANUAL)
- Dấu hiệu nhìn thấy khi phóng 100%: Chỉ nhìn thấy một phần nhỏ đầu của xe ở sát mép ảnh; phần lớn xe nằm ngoài khung hình nên không quan sát được đầy đủ.
- Giá trị `visibility`: unclear
- Giá trị `boundary`: truncated
- Trạng thái `review_state`: needs_review
- Lý do: Xe bị mép ảnh cắt nhiều và chỉ có một phần nhỏ được quan sát. Vì vậy bằng chứng về toàn bộ vật thể chưa đủ rõ để hoàn toàn tự tin, cần đánh dấu để xem lại.

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [ ] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 86 — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
