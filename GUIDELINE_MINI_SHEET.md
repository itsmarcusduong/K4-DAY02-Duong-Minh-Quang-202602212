# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Dương Minh Quang<br>
**MSSV:** 2A202602212<br>
**Hình thức:** Cá nhân<br>
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

- Ảnh và mã vật thể: Không có
- Dấu hiệu nhìn thấy: Không có
- Quy tắc áp dụng: Không có
- Quyết định: Không có
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Không có

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: Không có
- Dấu hiệu nhìn thấy: Không có
- Quy tắc áp dụng: Không có
- Quyết định: Không có
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Không có

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: 13
- Dấu hiệu nhìn thấy khi phóng 100%: Đuôi xe vuông, cao
- Giá trị `visibility`: unclear
- Giá trị `boundary`: truncated
- Trạng thái `review_state`: needs_review
- Lý do: Cần xem xét thêm vì có 1 khả năng thấp nhầm lẫn với van

- Ảnh và mã vật thể: 14, 15, 16, 17, 21, 23, 25, 40, 42, 74, 75, 81, 90, 104
- Dấu hiệu nhìn thấy khi phóng 100%: Thân xe không vuông, không phải là hình hộp, chiều dài xe ngắn
- Giá trị `visibility`: occluded
- Giá trị `boundary`: inside
- Trạng thái `review_state`: confident
- Lý do: Che phủ 1 phần thân xe nhưng chiều dài xe và thân xe giúp tự tin xác định đây là car

- Ảnh và mã vật thể: 26, 43, 106, 107, 108, 109, 110, 111, 112, 113, 114, 115, 116, 117, 120, 121
- Dấu hiệu nhìn thấy khi phóng 100%: Thân xe không vuông, không phải là hình hộp, chiều dài xe ngắn
- Giá trị `visibility`: unclear
- Giá trị `boundary`: inside
- Trạng thái `review_state`: confident
- Lý do: Chất lượng ảnh mờ, đầu xe bị che phủ 1 phần

- Ảnh và mã vật thể: 27, 30
- Dấu hiệu nhìn thấy khi phóng 100%: Thân xe không vuông, không phải là hình hộp, chiều dài xe ngắn
- Giá trị `visibility`: unclear
- Giá trị `boundary`: truncated
- Trạng thái `review_state`: needs_review
- Lý do: Chất lượng ảnh mờ, đầu xe bị che phủ 1 phần, xe bị mép ảnh cắt

- Ảnh và mã vật thể: 28, 52, 86, 103
- Dấu hiệu nhìn thấy khi phóng 100%: chiều cao xe đúng với xe bus
- Giá trị `visibility`: clear
- Giá trị `boundary`: truncated
- Trạng thái `review_state`: confident
- Lý do: Bị che phủ xe nhưng chiều cao và kiểu dáng xe giúp tự tin xác định đây là bus

- Ảnh và mã vật thể: 67, 72
- Dấu hiệu nhìn thấy khi phóng 100%: chiều cao xe đúng với car
- Giá trị `visibility`: clear
- Giá trị `boundary`: truncated
- Trạng thái `review_state`: confident
- Lý do: Bị che phủ 1 phần nhưng visibility clear giúp xác định rõ kiểu dáng xe để khẳng định đây là car

- Ảnh và mã vật thể: 85
- Dấu hiệu nhìn thấy khi phóng 100%: chiều cao xe đúng với car
- Giá trị `visibility`: occluded
- Giá trị `boundary`: truncated
- Trạng thái `review_state`: confident
- Lý do: Bị che phủ 1 phần nhưng độ cao và kiểu dáng xe giúp xác định rõ kiểu dáng xe để khẳng định đây là car

- Ảnh và mã vật thể: 101
- Dấu hiệu nhìn thấy khi phóng 100%: chiều cao xe đúng với xe bus
- Giá trị `visibility`: unclear
- Giá trị `boundary`: truncated
- Trạng thái `review_state`: needs_review
- Lý do: đối tượng quá mờ, không xác định rõ được nhãn nào nhưng xác suất cao là bus dựa vào chiều cao đầu xe

- Ảnh và mã vật thể: 118, 122
- Dấu hiệu nhìn thấy khi phóng 100%: chiều cao xe đúng với xe bus
- Giá trị `visibility`: unclear
- Giá trị `boundary`: inside
- Trạng thái `review_state`: confident
- Lý do: đối tượng quá mờ, không xác định rõ được nhãn nào nhưng xác suất cao là bus dựa vào chiều vào và hình dáng đầu xe

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [ ] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 122 — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
