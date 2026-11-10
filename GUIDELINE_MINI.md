# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp     | Định nghĩa của bạn                                                                    | Ví dụ/điểm phân biệt                                                                                                        |
| ------- | ------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `car`   | Xe cá nhân 4-7 chỗ hoặc taxi, kích thước nhỏ và gầm thấp nhất                         | Đầu và đuôi xe vuốt thon, không vuông vức. Các loại xe taxi và xe con chạy phổ biến trên đường                              |
| `truck` | Xe chở hàng hóa có buồng lái tách biệt hoàn toàn với thùng hàng phía sau              | Có khoảng hở rõ ràng giữa cabin và thùng chứa. Ví dụ như xe ben chở đất màu cam hoặc xe tải bưu điện thùng kín              |
| `bus`   | Xe buýt hoặc xe khách cỡ lớn nhất, có hình dáng khối hộp chữ nhật dài                 | Đầu xe phẳng vuông vức với dãy cửa sổ kính nối liền dọc thân xe. Ví dụ gồm các xe buýt công cộng và xe khách du lịch dài    |
| `van`   | Xe đa dụng cỡ trung thiết kế nguyên khối, thường dùng làm xe 16 chỗ hoặc chở hàng nhẹ | Đuôi xe vát thẳng đứng, to hơn xe con nhưng nhỏ hơn xe buýt. Ví dụ là các xe dạng hộp màu trắng hoặc bạc nhô cao hơn xe con |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Vẽ viền (bounding box) chạm đúng các mép ngoài cùng của xe (gương chiếu hậu, cản trước/sau, nóc xe, bánh xe dưới cùng). Không vẽ bao gồm phần bóng râm dưới mặt đường và phần hàng hoá mà xe chở.
- Vật thể bị che khuất: Vẽ box bao trọn phần xe có thể nhìn thấy. Nếu xe bị che lấp quá nhiều đến mức mất đặc điểm nhận dạng, không gán nhãn.
- Vật thể bị cắt bởi rìa ảnh: Chỉ vẽ box bám theo phần xe thực tế xuất hiện trong khung hình. Cạnh viền box đi sát theo cạnh của bức ảnh.
- Vật thể rất nhỏ/mơ hồ: Bỏ qua (không gán nhãn) đối với các xe ở quá xa phía chân trời, mờ nhòe, không đủ pixel để xác định chắc chắn đó là xe gì.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_008.jpg` | Xe tải ben màu đỏ chở đống đất/vật liệu cao vồng lên hẳn so với thành thùng xe | Gán nhãn `truck`, vẽ box ôm vừa khít cabin và thùng xe, không bao gồm đống đất vồng lên trên | Đúng định nghĩa lớp `truck` (xe tải chở hàng). Quy tắc chung yêu cầu box bám sát vật thể và loại trừ phần hàng hóa mà xe chở |
| `drive_037.jpg` | Xe bồn (xe xitec) chuyên dụng màu xanh lam có kích thước vừa, buồng lái và bồn chứa phía sau tách rời nhau | Gán nhãn `truck` | Mặc dù là xe bồn chứa chất lỏng (không phải thùng hàng thông thường), nhưng cấu trúc buồng lái tách biệt hoàn toàn với phần chuyên chở phía sau đáp ứng đúng tiêu chí "xe tải chở hàng, xe đầu kéo" của lớp `truck` |
| `drive_042.jpg` | Chiếc xe tải hạng nhẹ chở hàng hóa cồng kềnh (các bao tải lớn) chất cao vượt quá đầu xe | Gán nhãn `truck`, vẽ box chỉ lấy phần xe, bỏ qua đống hàng | Schema chỉ định rõ xe tải chở hàng thuộc lớp `truck`. Quy tắc chung yêu cầu box bám sát vật thể xe và không bao gồm phần hàng hóa chở thêm |
