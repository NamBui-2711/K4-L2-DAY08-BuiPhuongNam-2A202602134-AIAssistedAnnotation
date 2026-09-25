# Quét độc lập trước khi xem pre-label

Frame_0107: frame_0107 tên một ảnh trong `to_label/round1/images/train/frame_0107.jpg`

Số xe nhìn thấy bằng mắt: 26

Hai vị trí dễ bị AI bỏ sót hoặc vẽ sai, kèm mô tả xe: 
- Khoảng (x=275, y=281): xe rất nhỏ ở làn ngoài cùng bên trái, chỉ thấy một cặp đèn hậu đỏ trong vùng tối.
- Khoảng (x=1260, y=505): xe ở mép phải ảnh, bị cắt một phần khỏi khung hình; thân xe tối và chủ yếu nhận ra qua đèn hậu đỏ.

Chạy `python3 tools/lock_blind.py` ngay sau khi điền. Sau đó giữ file này nguyên vẹn.
