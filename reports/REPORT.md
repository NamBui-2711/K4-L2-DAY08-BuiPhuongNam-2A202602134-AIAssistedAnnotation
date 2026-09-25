# Báo cáo Lab Ngày 08: Học chủ động cho bộ phát hiện xe

Họ và tên: Bùi Phương Nam

Công cụ gán nhãn đã dùng: CVAT

## 1. Dữ liệu và cách chia tập

Tập chưa gán nhãn và tập kiểm thử được chia theo trục thời gian, có vùng đệm ở giữa, vì camera đứng yên và một chiếc xe có thể xuất hiện trong nhiều ảnh liên tiếp. Nếu chia ngẫu nhiên, các ảnh gần nhau của cùng một xe có thể vừa vào tập học vừa vào tập kiểm thử. Khi đó mô hình đã thấy gần như cùng cảnh trước khi được chấm, làm điểm kiểm thử cao hơn khả năng tổng quát thật.

## 2. Mô hình khởi đầu lạnh (cold start)

Ở vòng 0, mô hình `yolov8n cold start (COCO car+bus+truck)` dùng 0 ảnh train và 0 box train, đạt AP50 `0.771`, P `0.925`, R `0.489`, F1 `0.640`; recall xe nhỏ là `0.182`, xe vừa `0.547`, xe lớn `0.561`. Mô hình bỏ sót nhiều xe nhỏ ở xa, đặc biệt những xe chỉ còn vài đèn sáng, trong khi xe vừa và lớn được tìm thấy tốt hơn. Ảnh so sánh cho thấy ở các vùng xe nhỏ và xa có nhiều FN, còn một số box cũng lệch hoặc thiếu.

Tuy nhiên, cần rà lại nhãn tham chiếu trước khi kết luận mô hình sai. Nhãn test do máy tạo và chưa được người kiểm từng box, nên một box thiếu hoặc lệch trong nhãn tham chiếu có thể làm điểm đo thấp dù dự đoán có thể hợp lý.

## 3. Chiến lược chọn mẫu

Điểm chọn có dạng `score = W_U·U + W_A·A + W_D·D`: `U` đo mức mô hình không chắc, `A` đo số lượng box còn lưỡng lự, còn `D` ưu tiên ảnh khác thời gian với các ảnh đã chọn. `MIN_GAP_S` yêu cầu hai ảnh được chọn cách nhau ít nhất khoảng thời gian tối thiểu để tránh lấy nhiều ảnh gần như cùng một cảnh.

Ba frame trong lô 12 ảnh là `frame_0182.jpg` (điểm `0.9591`, hạng 1), `frame_0369.jpg` (điểm `0.9324`, hạng 2) và `frame_0380.jpg` (điểm `0.9170`, hạng 3). `frame_0372.jpg` có điểm `0.9101`, hạng 6, nhưng chỉ cách `frame_0369.jpg` 1.2 giây và bị loại vì cảnh gần trùng. Điểm cao chỉ cho biết mô hình đang bất định hoặc ảnh có nhiều trường hợp cần kiểm tra; nó không chứng minh rằng sửa ảnh đó chắc chắn sẽ cải thiện mô hình.

## 4. Các vòng học chủ động (active learning)

| Vòng | Ảnh train | Box train | AP50 | Delta AP50 so cold start | P | R | F1 | R small | R medium | R large |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 0 | 0 | 0 | 0.771 | - | 0.925 | 0.489 | 0.640 | 0.182 | 0.547 | 0.561 |
| 1 | 12 | 329 | 0.758 | -0.013 | 1.000 | 0.107 | 0.193 | 0.000 | 0.081 | 0.463 |

Trong vòng 1, tôi giữ nguyên 139 box, chỉnh sửa 15 box, xóa 15 box sai và thêm 175 box bị bỏ sót. Model ban đầu đề xuất 169 box, sau khi sửa còn 329 box, với accept rate 82%.

AP50 giảm `0.013` so với cold start, từ `0.771` xuống `0.758`. Recall giảm từ `0.489` xuống `0.107`; recall xe nhỏ giảm từ `0.182` xuống `0.000`, xe vừa từ `0.547` xuống `0.081`, và xe lớn từ `0.561` xuống `0.463`. Precision tăng lên `1.000`, nhưng mô hình dự đoán quá ít xe nên bỏ sót rất nhiều.

Trong `compare_round0.jpg`, ví dụ `frame_0050.jpg` có cold start TP 11, FP 2, FN 7. Trong `compare_round1.jpg`, cùng ảnh còn TP 1, FP 0, FN 17, nên nhiều xe trước đó được nhận diện đã biến mất sau fine-tune. Đây là thay đổi xấu cần kiểm tra về nhãn train, cách đóng gói dữ liệu và ngưỡng dự đoán.

Quét độc lập trong `BLIND_SCAN.md` ghi nhận ở `frame_0107.jpg` có 26 xe, trong đó có một xe rất nhỏ ở bên trái và một xe bị cắt ở mép phải dễ bị bỏ sót. `REVIEW_LOG.csv` ghi ba việc sửa cụ thể: thêm box ở `frame_0182.jpg`, chỉnh box ở `frame_0392.jpg` và xóa box trùng ở `frame_0150.jpg`. Đây là các quan sát và thao tác của người, khác với kết quả model sau khi train. Ca khó là xe xa chỉ còn đèn hậu hoặc xe bị cắt mép ảnh, vì ranh giới thân xe không rõ.

## 5. Kết luận và giới hạn

Vòng 1 kém hơn cold start: AP50 giảm `0.013`, recall và F1 cũng giảm mạnh. Tôi dừng train thêm để kiểm tra lại các box đã sửa, file nhãn được đóng gói, cách ánh xạ lớp và kết quả trên ảnh so sánh. Hai điểm còn yếu cho vòng sau là xe rất xa chỉ còn hai chấm đèn và xe bị cắt ở mép ảnh. Rà các trường hợp này tốn thời gian, đồng thời không nên chọn nhiều ảnh sát nhau vì chúng gần như cùng một cảnh.

Bộ kiểm thử chỉ có 20 ảnh, các xe quá nhỏ bị bỏ qua theo quy tắc, và nhãn tham chiếu do mô hình tạo chưa được người kiểm thủ công. Vì vậy, điểm số có thể chưa phản ánh hoàn toàn chất lượng thật. Nếu AP50 giảm sau một vòng khác, tôi sẽ kiểm tra trước các nhãn train, box trùng hoặc lệch, định dạng lớp, số lượng ảnh/box đã đóng gói và ngưỡng confidence trước khi train thêm.