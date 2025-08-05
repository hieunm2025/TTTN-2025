hydra -l [username] -P /etc/passwords-list.txt 192.168.0.115 ftp
Giai đoạn 1 - Rà quét (Reconnaissance): Dùng nmap để phát hiện cổng FTP mở.
Giai đoạn 2 - Phân tích gói tin (Packet Analysis): Dùng Wireshark phân tích file Capture.pcapng để tìm tên người dùng jenny.
Giai đoạn 3 - Tấn công mật khẩu (Brute-force): Dùng lệnh hydra đã được sửa đúng cú pháp để tìm ra mật khẩu kelly.
Giai đoạn 4 - Hậu khai thác (Post-exploitation): Đăng nhập FTP, tải lên một backdoor shell.php, và có được một reverse shell, sau đó leo thang đặc quyền lên root.
