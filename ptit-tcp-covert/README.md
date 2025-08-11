Nội dung và hướng dẫn thực hiện bài thực hành

- Chủ đề bài lab: Các kĩ thuật ẩn danh

Mục đích
- Bài lab xây dựng với mục đích cho sinh viên biết TCP/IP header có các trường có thể thay đổi mà không ảnh hưởng. Từ đó truyền lưu lượng bí mật bên trong các trường không sử dụng của tiêu đề TCP và IP.

Yêu cầu đối với sinh viên
- Có kiến thức cơ bản về hệ điều hành Linux

Nội dung thực hành
Tải bài lab:
imodule https://github.com/longtran07/ptit-tcp-covert/raw/main/imodule.tar

Khởi động bài lab:
Vào terminal, gõ:
labtainer -r ptit-tcp-covert

 (chú ý: sinh viên sử dụng mã sinh viên của mình để nhập thông tin email người thực hiện bài lab khi có yêu cầu, để sử dụng khi chấm điểm)

Sau khi khởi động xong hai terminal ảo sẽ xuất hiện, mỗi một rerminal sẽ có hai tab, một cái là đại diện cho máy gửi: send, một cái là đại diện cho máy nhận: receive. Biết rằng 2 máy nằm cùng mạng LAN.

Trên terminal send , receive sử dụng lệnh “ifconfig”, xác định địa chỉ IP và địa chỉ mạng LAN.
Trên máy send, thêm vào cuối file message.txt được tạo sẵn chuỗi: “Secret Message!”
( Lưu ý, không xóa mã băm được tạo sẵn)
Config máy receive
Sử dụng quyền sudo và mở tcpdump để theo dõi
sudo tcpdump -nvvX port 8888 -i lo

Mở terminal thứ hai Lưu ý: Trường hợp gặp lỗi về cc lệnh, hãy cài đặt trình biên dịch: sudo apt install gcc
Biên dịch file convert_tcp.c
cc -o covert_tcp covert_tcp.c

Thiết lập thiết lập trình nghe
Source_port using port 9999 
Dest_port using port 8888
File receive.txt cần điền đầy đủ đường dẫn tạo ra file
sudo ./covert_tcp -dest <ip máy receive >-source <ip máy send>-source_port <port>-dest_port <port> -server -file receive.txt

Config máy send
Biên dịch file convert_tcp.c tương tự như trên
Mở wireshark trên 1 tab và đặt lắng nghe bắt lưu lượng ở tất cả các interface
Thiết lập trình gửi trên tab thứ hai
Port cần config ngược lại so với máy receive
message.txt cần điền đường dẫn đến file thực tế
sudo ./covert_tcp -dest <ip máy receive>-source <ip máy send>-source_port <>-dest_port <>-file message.txt

Trên máy receive:
Trên máy receive dừng tcpdump --> thấy không có gói nào bị bắt trong mạng.
Sử dụng ls kiểm tra các file đã tạo ra, và cat file receive.txt để kiểm tra nội dung
Trên máy send mở wireshark
Search các gói tin tcp theo dõi kiểm tra giao tiếp giữa 2 máy send và receive. Quan sát để thấy từng ký tự của chuỗi tin nhắn được gửi dưới dạng các gói riêng lẻ
covert_tcp thay đổi tiêu đề của gói TCP và thay thế nó bằng các ký tự của chuỗi từng ký tự một để gửi tin nhắn mà không bị phát hiện.
Kiểm tra kết quả bằng lệnh :
checkwork

Dừng bài lab