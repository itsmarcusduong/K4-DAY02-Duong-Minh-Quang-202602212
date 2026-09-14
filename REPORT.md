# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Dương Minh Quang<br>
**MSSV:** 2A202602212<br>
**Hình thức:** Cá nhân<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh: drive_022, drive_033, drive_038, drive_008
- Số vật thể thực tế: 122 hộp (YOLO và CVAT cùng ghi nhận 122). Con số này vượt mục tiêu khối lượng 40–60 nên cần rà lại phạm vi, nhưng không được tự xóa hộp chỉ để đạt khoảng mục tiêu.
- Mã SHA-256 của gói YOLO của bạn: 29d4f2cf4bcb0bc065c33afbd909a231b85818052ee6f7855041b26d551e1ec2
- Mã SHA-256 của gói CVAT gốc của bạn: aa0299a6951e6bf1bffb8067e43ba85bfb2729d372402052e9df9ee7b82f8aeb
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp: teaching_reference
- Mã SHA-256 của gói đối chiếu: c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu:

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu: Tôi hoàn thành và kiểm tra hai gói xuất của mình trước khi chọn hình thức cá nhân và tải bộ tham chiếu. Trong notebook, gói YOLO và CVAT của tôi được tải/kiểm ở các cell 5 và 8, huấn luyện thử diễn ra ở cell 10; đến cell 11 tôi mới chọn nguồn teaching_reference và cell 12 mới tải bộ đối chiếu. Mã SHA-256 gói của tôi cũng khác mã gói đối chiếu, nên nguồn đối chiếu không phải bản sao của bài riêng.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| drive_022 – xe dài ở tiền cảnh | bus | Thân xe khách dài, nhiều cửa sổ, hình dáng phương tiện chở nhiều người | Gán bus khi thấy thân xe khách dài và nhiều cửa sổ/hàng ghế; không gán van |
| drive_038 – xe công vụ trắng giữa-phía dưới | truck | Có cabin và phần sàn/thiết bị công vụ tách rõ ở phía sau | Gán truck khi có thùng, ben, sàn hàng hoặc thiết bị công vụ rõ |
| drive_008 – sedan trắng ở gần phía dưới | car | Thân sedan, khoang hành khách điển hình, không có thùng hàng hoặc thân hộp | Sedan/hatchback/SUV/taxi thuộc car
| drive_008 – xe thân hộp nhỏ gần giữa ảnh | van | Thân xe ngắn, cao và kín, không dài như bus, không có thùng tách như truck | Gán van cho xe thân hộp nhỏ dùng chở người/hàng

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau: Một phương tiện có lớp bus, còn visibility=occluded, boundary=inside và review_state=needs_review là các thuộc tính mô tả tình trạng quan sát/kiểm tra của chính chiếc bus. Đổi thuộc tính không biến bus thành car, truck hay van. Gói YOLO lưu lớp và hộp nhưng không giữ ba thuộc tính này, vì vậy cần thêm gói CVAT gốc.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| Có 122 hộp, trong đó nhiều hộp bao các phương tiện rất nhỏ/xa; 77 hộp phía tôi không ghép được với bộ tham chiếu | Phạm vi | Cảnh báo 122 hộp vượt mục tiêu 40–60; xem comparison_overlay.png thấy nhiều hộp đỏ nhỏ, nhất là ở drive_033 và drive_038, không có hộp xanh tương ứng | Rà từng hộp ở kích thước 100%; chỉ giữ phương tiện thuộc bốn lớp và có đủ bằng chứng phân lớp. Không đoán vật thể quá nhỏ/mờ; ghi lý do vào nhật ký quyết định. Không xóa tự động toàn bộ hộp không ghép được vì bộ tham chiếu không phải ground truth sản xuất |
| Hai gói xuất có nguy cơ khác trạng thái | Thuộc tính/hình học | Kiểm tra chéo YOLO–CVAT | Kết quả cuối cùng đạt: ghép 122/122 hộp, IoU nhỏ nhất 0.9998598556 ≥ ngưỡng kỹ thuật 0.995; cả hai gói cùng trạng thái |
| Một số hộp có thể thừa nền hoặc ước lượng phần bị che | Hình học | Phóng ảnh và so cạnh hộp với phần phương tiện nhìn thấy | Vẽ sát phần nhìn thấy, không ước lượng phần bị xe khác che; hộp được phép chạm mép nếu vật thể bị cắt nhưng vẫn đủ bằng chứng phân lớp |

- Số hộp `needs_review` trước và sau khi kiểm: 5, 0
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: Với xe rất nhỏ ở xa trong drive_033, nếu phóng 100% vẫn không phân biệt được car/van, tôi đặt visibility=unclear, review_state=needs_review, ghi mã ảnh/vị trí và hỏi Lab Coach. Tôi không đoán lớp chỉ để tăng số hộp.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: 0 0.263086 0.574172 0.123484 0.075531
- Tên lớp và tọa độ điểm ảnh `xyxy`: class_id=0 tương ứng car; bbox_xyxy ≈ [128.9, 343.3, 207.9, 391.6] pixel trên ảnh 640×640.
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học? Tâm hộp xấp xỉ (0.2631, 0.5742), chiều rộng 0.1235 và chiều cao 0.0755, đều được chuẩn hóa theo kích thước ảnh.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: drive_022, drive_033, drive_038
- Mã ảnh thẩm định: drive_008
- Mô tả một dự đoán trong `detect_result.jpg`: Với ngưỡng confidence 0.25, ảnh drive_008 được hiển thị nhưng không có bounding box/nhãn dự đoán nào xuất hiện. Vì vậy không có một prediction cụ thể vượt ngưỡng để trích dẫn; đây là kết quả “không phát hiện”.

Gợi ý cần kiểm lại: Kiểm tra liệu 3 ảnh train có quá ít và phân bố lớp có lệch mạnh hay không (train có 73 car, 4 truck, 12 bus, 1 van), nhãn drive_008 có nhất quán không, các hộp xe nhỏ có làm tín hiệu huấn luyện nhiễu không, và ngưỡng confidence khi dự đoán có quá cao không.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Kiểm tra liệu 3 ảnh train có quá ít và phân bố lớp có lệch mạnh hay không (train có 73 car, 4 truck, 12 bus, 1 van), nhãn drive_008 có nhất quán không, các hộp xe nhỏ có làm tín hiệu huấn luyện nhiễu không, và ngưỡng confidence khi dự đoán có quá cao không.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Chạy lại inference với cấu hình/ngưỡng được ghi rõ và xuất JSON/TXT score; nếu xuất hiện prediction hợp lệ nhưng ảnh render không vẽ, nguyên nhân là bước hiển thị. Nếu nhiều lần huấn luyện trên tập dữ liệu lớn, tách biệt và đã QC vẫn không phát hiện, khi đó mới có bằng chứng mạnh hơn về vấn đề mô hình/dữ liệu
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế? Chỉ có 4 ảnh (3 train, 1 val) và 122 nhãn, ảnh val cũng tham gia đối chiếu thủ công; mẫu quá nhỏ, cùng miền cảnh và không có test set độc lập. Các chỉ số như mAP50≈0.00722 và mAP50-95≈0.003 chỉ là tín hiệu chẩn đoán của lần chạy này, không ước lượng được khả năng tổng quát hay an toàn khi triển khai.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 45
- IoU trung bình và trung vị: 0.863158 và 0.884505
- Mức đồng thuận lớp: 0.711111 (khoảng 71.11% trong 45 cặp ghép)
- Số hộp phía bạn không ghép được: 77
- Số hộp phía đối chiếu không ghép được: 5
- Một điểm khác biệt cụ thể: Trong drive_033, ảnh phủ cho thấy nhiều hộp đỏ của tôi quanh các phương tiện rất nhỏ ở xa, đặc biệt gần phần trên của đường, nhưng không có hộp xanh đối chiếu tương ứng. Đây là khác biệt về phạm vi; chỉ từ ảnh phủ chưa thể kết luận từng hộp nào sai.
- Quy tắc hoặc hành động sửa phát sinh: Rà lại toàn bộ 77 hộp không ghép ở zoom 100%; loại hộp nếu đối tượng quá nhỏ/mờ đến mức không thể phân lớp có căn cứ hoặc nằm ngoài bốn lớp. Giữ và ghi lý do nếu phương tiện vẫn đủ bằng chứng theo guideline. Với 5 hộp chỉ có ở phía đối chiếu, kiểm ảnh gốc để xác định tôi có bỏ sót hay bộ đối chiếu gán rộng hơn.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng? Hai nguồn có thể cùng lặp một lỗi lớp/phạm vi; IoU cao chỉ cho biết hình học gần nhau ở các hộp đã ghép; class agreement không đánh giá các hộp không ghép; và bộ tham chiếu được ghi rõ chỉ hỗ trợ phản hồi, không phải kết luận chất lượng sản xuất. Hơn nữa, 71.11% đồng thuận lớp vẫn cho thấy khoảng 28.89% cặp ghép khác lớp.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach: Hai gói YOLO và CVAT cùng chứa 122 hộp và kiểm tra chéo ghép đủ 122 hộp với IoU nhỏ nhất 0.9998598556, cho thấy hai export đến từ cùng trạng thái annotation. Kết quả đối chiếu độc lập (45 hộp ghép, IoU trung bình 0.863158, 77 hộp phía tôi không ghép) chỉ ra rõ phần cần rà lại là quy tắc phạm vi.

Với các phương tiện rất nhỏ nhưng vẫn có thể đoán loại khi phóng to, guideline có yêu cầu kích thước tối thiểu hoặc dấu hiệu bắt buộc nào để giữ nhãn, hay phải loại nếu không đủ bằng chứng chắc chắn?

