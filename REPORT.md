# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Mai Lưu Ly  
**MSSV:** 2A202602157  
**Hình thức:** cá nhân  
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33`
- Bốn mã ảnh: `drive_008`, `drive_022`, `drive_033`, `drive_038`
- Số vật thể thực tế sau khi sửa: `57` (bản trước khi sửa có `67`)
- Mã SHA-256 của gói YOLO cập nhật: `aefb54ac7a7e89622d0316e67ed63ebebcd6d44b01042f59c2bd016fa576c251`
- Mã SHA-256 của gói CVAT gốc cập nhật: `be7a31d5c68426a7d7055c9f1b5fedf08c5bf498de2e8959938870e4e403aeae`
- Nguồn đối chiếu: bộ nhãn đối chiếu do Lab Coach cấp (`day2-teaching-reference.zip`)
- Mã SHA-256 của gói đối chiếu: `c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b`
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: mã lần phát `day2-reference-4img-v1`; thời điểm nhận `14/09/2026 15:32:52` theo thời gian file trên máy.

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Tôi hoàn thành việc gán nhãn cả bốn ảnh, tự kiểm tra và xuất riêng hai gói YOLO và CVAT trước khi nhận bộ nhãn tham chiếu từ Lab Coach. Gói YOLO của tôi đã được khóa và ghi nhận bằng mã SHA-256 trước khi thực hiện đối chiếu. Tôi không xem, sao chép hoặc sử dụng nhãn tham chiếu trong quá trình gán nhãn ban đầu. Bộ tham chiếu chỉ được sử dụng sau đó để phân tích sự khác biệt về phạm vi, lớp và hình học bounding box. Vì vậy, kết quả ban đầu vẫn phản ánh quyết định gán nhãn độc lập của tôi.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| `drive_022`, xe lớn ở tiền cảnh | `bus` | Thân xe khách dài, nhiều cửa sổ liên tiếp và có hình dáng xe chở nhiều hành khách | Gán `bus` khi thấy thân xe khách dài, nhiều cửa sổ hoặc nhiều hàng ghế; không gán thành `van` chỉ dựa vào hình dáng thân hộp |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Một xe vẫn có lớp `car` vì hình dáng cho thấy đó là ô tô con, nhưng có thể mang thuộc tính `visibility=occluded` nếu bị xe khác che một phần. Lớp mô tả loại vật thể, còn thuộc tính mô tả tình trạng quan sát, quan hệ với mép ảnh và mức độ cần xem lại; thuộc tính không làm thay đổi lớp của xe.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Bản trước có `67` hộp, gồm 10 hộp rất nhỏ hoặc mờ chưa đủ bằng chứng phạm vi | phạm vi/độ đầy đủ | Rà ảnh ở mức phóng 100% và đối chiếu các hộp không ghép; xác định các hộp nhỏ không đủ căn cứ phân lớp | Xóa 10 hộp không đủ bằng chứng trong CVAT rồi xuất lại cả hai định dạng từ cùng trạng thái. Bản cập nhật còn `57` hộp, nằm trong mục tiêu 40–60; không sửa trực tiếp TXT hoặc XML |

- Số hộp `needs_review` trước và sau khi kiểm: bản trước có `2`; bản cập nhật còn `1` hộp trên tổng số `57` hộp.
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: hộp `car` thứ 14 trong `drive_038`, tọa độ khoảng `(218.01, 103.09, 270.03, 158.77)`, có `visibility=occluded`, `boundary=inside`, `review_state=needs_review`. Tôi giữ hộp để xin Lab Coach xác nhận vì xe bị che một phần, thay vì tự đổi lớp hoặc xóa khi chưa đủ bằng chứng.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `2 0.382828 0.726063 0.453781 0.376250`
- Tên lớp và tọa độ điểm ảnh `xyxy`: lớp `2` là `bus`; `xyxy = [99.8, 344.7, 390.2, 584.7]` pixel trên ảnh `drive_022` kích thước 640 × 640.
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học? Một dòng có đủ năm giá trị và tọa độ nằm trong khoảng hợp lệ chỉ chứng minh cú pháp có thể đọc được. Người gán nhãn vẫn có thể chọn nhầm mã lớp, gán một vật thể ngoài phạm vi, bỏ sót vật thể hoặc vẽ hộp chứa quá nhiều nền hay cắt mất phần nhìn thấy của xe.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022`, `drive_033`, `drive_038`
- Mã ảnh thẩm định: `drive_008`
- Thông số lần chạy: Ultralytics `8.4.145`, seed `42`, `8` epoch, thiết bị `CPU`.
- Mô tả một dự đoán trong `detect_result.jpg`: trên ảnh `drive_008`, ảnh kết quả không hiển thị bounding box dự đoán rõ ràng ở ngưỡng tin cậy `0.25`, mặc dù ảnh có nhiều phương tiện lớn như hai xe buýt, xe tải và nhiều ô tô.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Cần kiểm tra lại tính đầy đủ và nhất quán của nhãn, đặc biệt là các lớp ít mẫu như `truck`, `bus`, `van`, đồng thời kiểm tra số hộp nhỏ hoặc bị che và sự chênh lệch giữa 57 hộp cập nhật với 50 hộp tham chiếu.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Các confidence score thô, kết quả dự đoán ở các ngưỡng khác, hoặc đánh giá trên một tập dữ liệu độc lập lớn hơn có thể cho thấy mô hình vẫn phát hiện được vật thể nhưng các hộp không được hiển thị ở ngưỡng `0.25`. Việc rà trực tiếp nhãn gốc cũng có thể cho thấy vấn đề đến từ thời lượng huấn luyện quá ngắn chứ không phải lỗi nhãn.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế? Chỉ có ba ảnh huấn luyện và một ảnh thẩm định, đều thuộc cùng một bộ dữ liệu nhỏ; tám epoch không đủ đại diện cho nhiều điều kiện giao thông, góc nhìn, ánh sáng và thời tiết. Vì vậy IoU, mAP hay ảnh dự đoán ở đây chỉ là tín hiệu chẩn đoán đường ống kỹ thuật, không chứng minh khả năng tổng quát hóa hoặc mức độ an toàn khi triển khai.

## 6. Đối chiếu nhãn

- Số hộp ghép được: `40`
- IoU trung bình và trung vị: trung bình `0.655811`; trung vị `0.672862`
- Mức đồng thuận lớp: `0.725`, tương đương `72.5%` trong các cặp đã ghép
- Số hộp phía tôi không ghép được: `17`
- Số hộp phía đối chiếu không ghép được: `10`
- Một điểm khác biệt cụ thể: tại `drive_022`, hộp dòng 1 của tôi được gán `bus` nhưng hộp ghép tương ứng dòng 2 của nguồn tham chiếu được gán `van`; IoU của cặp là `0.834904`. Đây là trường hợp hình học tương đối gần nhau nhưng quyết định lớp khác nhau.
- Quy tắc hoặc hành động sửa phát sinh: phóng ảnh lên 100% và dựa vào các dấu hiệu cấu trúc nhìn thấy. Chỉ gán `bus` khi có thân xe khách dài, nhiều cửa sổ hoặc dấu hiệu nhiều hàng ghế; gán `van` khi xe có thân hộp nhỏ, kín và không có hình thái xe buýt. Nếu vẫn thiếu bằng chứng, giữ `review_state=needs_review`, ghi lý do và xin Lab Coach xem lại trong CVAT; không sửa trực tiếp file TXT.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng? Hai nguồn có thể cùng bỏ sót một vật thể, cùng áp dụng sai quy tắc hoặc cùng vẽ hộp sai nhưng vẫn đồng thuận với nhau. Chỉ số đồng thuận cũng chỉ được tính trên các hộp ghép được và không tự giải thích 17 hộp của tôi cùng 10 hộp tham chiếu chưa ghép được.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [X] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình — cần kiểm lại kho GitHub cuối cùng; chỉ đưa thư mục `day2_lab_outputs`, báo cáo và phiếu quy tắc lên kho.
- [x] Không phát hiện dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập trong bộ minh chứng đầu ra cần nộp.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất là hai gói cập nhật YOLO và CVAT có cùng 57 hộp, cùng bốn ảnh và đạt kiểm tra nhất quán chéo với 57 hộp được ghép, IoU nhỏ nhất khoảng `0.999954`; điều này chứng minh hai định dạng được xuất từ cùng trạng thái CVAT. Câu hỏi còn lại cho Lab Coach là: với 17 hộp của tôi không ghép được và tổng số 57 hộp vẫn cao hơn 50 hộp tham chiếu, những vật thể nhỏ hoặc mờ nào vẫn đủ bằng chứng để giữ lại theo quy tắc phạm vi của bài?
