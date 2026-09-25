# Vì sao chọn lô này?

Nếu chỉ được sửa 5 ảnh, tôi chọn `frame_0182.jpg` (hạng 1, điểm 0.9591, thời điểm 72.8 giây), `frame_0369.jpg` (hạng 2, điểm 0.9324, thời điểm 147.6 giây), `frame_0380.jpg` (hạng 3, điểm 0.9170, thời điểm 152.0 giây), `frame_0326.jpg` (hạng 4, điểm 0.9155, thời điểm 130.4 giây) và `frame_0331.jpg` (hạng 5, điểm 0.9154, thời điểm 132.4 giây). Đây là năm ảnh đứng đầu danh sách, có nhiều xe và nhiều khung AI còn lưỡng lự. `frame_0182.jpg` và `frame_0187.jpg` chỉ cách nhau 2 giây, nên tôi chọn ảnh có điểm cao hơn là `frame_0182.jpg` để tránh sửa hai cảnh gần trùng nhau.

Ba frame thuộc lô 12 ảnh model chọn là `frame_0182.jpg`, `frame_0369.jpg` và `frame_0380.jpg`. Trong CSV, chúng lần lượt có điểm 0.9591, 0.9324 và 0.9170; số khung dự đoán là 28, 43 và 40, với 18, 16 và 15 khung không chắc. Trên contact sheet, cả ba đều có nhiều xe ở các làn gần và xa, nhiều đèn pha/đèn hậu và xe nhỏ ở phía xa, nên có nhiều trường hợp cần kiểm tra khung.

`frame_0372.jpg` có điểm khá cao 0.9101, đứng hạng 6, nhưng không được chọn vì thời điểm 148.8 giây chỉ cách `frame_0369.jpg` 1.2 giây và cảnh gần như giống nhau. Chọn `frame_0369.jpg` giúp tránh dành công sức cho hai ảnh liên tiếp có ít thông tin mới.

Phép chọn này chỉ cho biết ảnh nào có điểm bất định cao, nhiều khung dự đoán còn lưỡng lự và khác thời gian với các ảnh khác. Nó chưa chứng minh chất lượng mô hình hoặc cho thấy sửa ảnh nào chắc chắn sẽ làm mô hình tốt hơn; điều đó cần được đánh giá bằng nhãn đã kiểm tra và bộ kiểm tra riêng.
