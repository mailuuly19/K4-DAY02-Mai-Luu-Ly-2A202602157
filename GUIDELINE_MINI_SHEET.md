# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Mai Lưu Ly  
**MSSV:** 2A202602157  
**Hình thức:** cá nhân  
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

- Ảnh và mã vật thể: `drive_022`, xe lớn ở tiền cảnh, hộp `xyxy ≈ (99.80, 344.28, 390.22, 585.08)`.
- Dấu hiệu nhìn thấy: thân xe khách dài, kích thước lớn và có nhiều cửa sổ liên tiếp; hình dáng phù hợp xe chở nhiều hành khách hơn xe van nhỏ.
- Quy tắc áp dụng: gán `bus` khi nhìn thấy thân xe khách dài, nhiều cửa sổ hoặc dấu hiệu nhiều hàng ghế; không phân loại chỉ dựa vào màu hay kích thước hộp.
- Quyết định: `bus`, `visibility=clear`, `boundary=inside`, `review_state=confident`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Phóng ảnh lên 100%, giữ `review_state=needs_review`, ghi lại dấu hiệu còn thiếu và xin Lab Coach xác nhận; không tự đoán hoặc sửa trực tiếp file nhãn.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_038`, xe công vụ màu trắng ở gần giữa phía dưới, hộp `xyxy ≈ (269.83, 336.89, 484.22, 510.09)`.
- Dấu hiệu nhìn thấy: xe có thiết bị công vụ và kết cấu làm việc tách biệt phía sau cabin; không phải thân kín một khối như van và không có hình dáng ô tô con.
- Quy tắc áp dụng: gán `truck` khi nhìn thấy thùng, sàn hàng hoặc thiết bị công vụ rõ ràng; không gán `van` chỉ vì xe có cabin nhỏ.
- Quyết định: `truck`, `visibility=clear`, `boundary=inside`, `review_state=confident`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Phóng ảnh lên 100%, so sánh phần sau cabin với tiêu chí `truck/van`, đánh dấu `needs_review` và xin Lab Coach hỗ trợ nếu kết cấu vẫn không rõ.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_038`, hộp `car` thứ 14, `xyxy ≈ (218.01, 103.09, 270.03, 158.77)`.
- Dấu hiệu nhìn thấy khi phóng 100%: vật thể nằm hoàn toàn trong ảnh nhưng bị phương tiện khác che một phần; vẫn thấy dấu hiệu của ô tô con nhưng chưa đủ bằng chứng để kết luận hoàn toàn chắc chắn.
- Giá trị `visibility`: `occluded`.
- Giá trị `boundary`: `inside`.
- Trạng thái `review_state`: `needs_review`.
- Lý do: vật thể không bị mép ảnh cắt nhưng bị che một phần nên bằng chứng thị giác còn hạn chế. Tôi giữ trạng thái cần xem lại để xin xác nhận thay vì đổi lớp không có căn cứ. Bản cập nhật còn một hộp `needs_review`, chính là hộp này.

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính; file audit cập nhật xác nhận đủ 57 giá trị cho từng thuộc tính.
- [x] Đã rà mọi hộp `needs_review`; giảm từ hai xuống một hộp và giữ lại hộp cần hỗ trợ vì chưa đủ bằng chứng để kết luận chắc chắn.
- [x] Đã hoàn thành ba tình huống và khóa bản xuất riêng trước khi dùng nguồn đối chiếu.
- [x] Không áp dụng hình thức theo cặp; bài này làm cá nhân (`SOLO`).
- [x] Bài riêng đã được kiểm và ghi SHA-256 trước khi nhận bộ tham chiếu của Lab Coach.
- [x] Số vật thể thực tế sau khi sửa: `57` (trước khi sửa: `67`) — nằm trong mục tiêu khối lượng 40–60 và phản ánh việc loại các hộp không đủ bằng chứng trong CVAT.
