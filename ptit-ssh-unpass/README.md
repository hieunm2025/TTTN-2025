Nội dung và hướng dẫn thực hiện bài thực hành

Mục đích
Giúp sinh viên hiểu về các phương pháp xác thực trong dịch vụ SSH
Yêu cầu đối với sinh viên
Hiểu cách thức truy cập từ xa từ client đến server
Các phương pháp xác thực người dùng trong dịch vụ SSH, gồm xác thực sử dụng mật khẩu và public key
Nội dung thực hành
Tải bài lab:
            imodule https://github.com/ctdinh10x/ptit-ssh-unpass/raw/main/imodule.tar

Khởi động bài lab: Vào terminal, gõ:
              labtainer ptit-ssh-unpass

 (chú ý: sinh viên sử dụng MSV của mình để nhập thông tin email người thực hiện bài lab khi có yêu cầu, để sử dụng khi chấm điểm)

1. Sau khi khởi động xong hai terminal ảo sẽ xuất hiện, một cái là đại diện cho máy khách: client, một cái là đại diện cho máy chủ: server. Biết rằng 2 máy nằm cùng mạng LAN 172.20.0.0/24.

client có địa chỉ IP là 172.20.0.10

server có địa chỉ IP là 172.20.0.20

2. Từ client truy cập vào server bằng ssh và sử dụng mật khẩu là ubuntu:

ssh ubuntu@<IP Server>

3. Thêm các dòng config vào cuối file /etc/ssh/sshd_config trên server

PubkeyAuthentication yes
PasswordAuthentication no
PermitRootLogin no

4. Gõ exit để thoát khỏi phiên ssh. Từ Client tiến hành ssh lại truy cập vào server và nhận thấy bị từ chối kết nối do ssh server không chấp nhận xác thực sử dụng mật khẩu.

5. Do server không chấp nhận xác thực sử dụng mật khẩu nên client tiến hành gen key để chuẩn bị gửi sang cho server

ssh-keygen

6. Client gửi key sang cho server nhưng không thành công do server bắt buộc phải copy key thủ công để gửi sang

ssh-copy-id

7. Client tiến hành cat key ra để copy sang server

cat ~/.ssh/id_rsa.pub

8. Trên Server tiến hành tạo file ~/.ssh/authorized_keys và add key của client vào file

sudo mkdir ~/.ssh/
sudo nano ~/.ssh/authorized_keys
(Copy toàn bộ key từ terminal client và paste sang server, lưu file ~/.ssh/authorized_keys)

9. Sau khi được add public key của mình vào server, client tiến hành ssh lại vào server mà không cần mật khẩu

ssh ubuntu@<IpServer>

Kết thúc bài lab:

Trên terminal đầu tiên sử dụng câu lênh sau để kết thúc bài lab:

stoplab

Khi bài lab kết thúc, một tệp zip lưu kết quả được tạo và lưu vào một vị trí được hiển thị bên dưới stoplab.

 

Khởi động lại bài lab:

Trong quá trình làm bài sinh viên cần thực hiện lại bài lab, dùng câu lệnh:

labtainer -r ptit-ssh-unpass

Trong trường hợp dịch vụ SSH bị xung đột với dịch vụ khác (ví dụ như xinetd), cần tắt dịch vụ xinetd để không ảnh hưởng đến SSH:

Mở file cấu hình của xinetd:

sudo nano /etc/xinetd.d/ssh
Sửa dòng disable = no thành disable = yes để tắt dịch vụ SSH của xinetd.

Lưu và thoát khỏi file.