
---

**Giới thiệu chung về bài thực hành**

* **Tên bài thực hành:** Tìm hiểu về Proxy

**Mục đích:**

* Giúp sinh viên làm quen với hoạt động của proxy, cài đặt và cấu hình proxy server sử dụng Squid.
* Hiểu và thực hành cách quản lý quyền truy cập qua proxy, sử dụng blacklist và whitelist để kiểm soát lưu lượng mạng.

**Yêu cầu đối với sinh viên:**

* Có kiến thức cơ bản về Wireshark và bảo mật mạng.

---

**Nội dung và hướng dẫn bài thực hành**

**1. Tải bài lab**

* Nếu chưa tải bài lab, sử dụng lệnh sau để tải về:

  ```
  imodule https://github.com/thang010501/ptit-proxy/raw/main/imodule.tar
  ```

**2. Khởi động bài lab**

* Mở terminal và gõ lệnh sau:

  ```
  labtainer -r ptit-proxy
  ```
* Lưu ý: Sinh viên nhập mã sinh viên (MSV) khi có yêu cầu để sử dụng khi chấm điểm.

**3. Cài đặt Proxy Server (Cài đặt Squid và khởi động)**

* Mở cấu hình Squid:

  ```
  sudo nano /etc/squid/squid.conf
  ```
* Thêm các dòng cấu hình sau vào `squid.conf`:

  ```bash
  http_port 3000                  # Cổng lắng nghe yêu cầu HTTP
  visible_hostname nhom1          # Tên máy chủ proxy
  acl localnet src 172.20.0.0/24  # Địa chỉ IP của mạng nội bộ
  http_access deny blacklist      # Cấm truy cập từ blacklist
  http_access allow localnet      # Cho phép truy cập từ mạng nội bộ
  http_access deny all            # Cấm tất cả các yêu cầu khác
  ```
* Kiểm tra cấu hình:

  ```
  sudo squid -k parse
  ```
* Khởi động dịch vụ Squid:

  ```
  sudo systemctl start squid
  ```

**4. Cấu hình Proxy Client**

* Mở Firefox trên máy client và truy cập vào phần **Network Settings**.
* Chọn **Manual proxy configuration**, nhập địa chỉ IP `172.20.0.2` và cổng `3000`.
* Chọn **Use this proxy server for all protocols** và ấn **Save**.

**5. Kiểm tra kết nối Proxy**

* Trên máy proxy, sử dụng lệnh sau để lắng nghe kết nối:

  ```
  netstat -an | grep ESTABLISHED | grep :3000
  ```

---

**Cấu hình Logging**

**6. Cấu hình Logging trong Squid**

* Mở file cấu hình Squid:

  ```
  sudo nano /etc/squid/squid.conf
  ```
* Thêm các dòng sau để cấu hình logging:

  ```bash
  cache_dir ufs /var/spool/squid 100 16 256
  maximum_object_size 200 MB
  access_log /var/log/squid/access.log
  cache_log /var/log/squid/cache.log
  cache_store_log /var/log/squid/store.log
  ```
* Khởi động lại Squid:

  ```
  sudo systemctl restart squid
  ```
* Xem log truy cập của proxy:

  ```
  sudo tail -f /var/log/squid/access.log
  ```

---

**Quản lý truy cập:**

**7. Tạo Blacklist để cấm truy cập web đen**

* Tạo file `blacklist.txt` để chứa các trang web cần cấm, ví dụ:

  ```
  sudo nano /etc/squid/blacklist.txt
  ```

  * Nhập `www.facebook.com` vào file.
* Mở lại cấu hình Squid và thêm dòng sau:

  ```bash
  acl blacklist url_regex "/etc/squid/blacklist.txt"
  http_access deny blacklist
  ```
* Khởi động lại Squid:

  ```
  sudo systemctl restart squid
  ```
* Xem log hoạt động của Squid:

  ```
  sudo tail -f /var/log/squid/access.log
  ```
* Truy cập vào `www.facebook.com` từ máy client để kiểm tra cấm truy cập.

**8. Tạo Whitelist để chỉ cho phép truy cập trang web nhất định**

* Tạo file `whitelist.txt` và thêm các trang web được phép truy cập:

  ```
  sudo nano /etc/squid/whitelist.txt
  ```

  * Nhập `www.facebook.com` vào file.
* Mở lại cấu hình Squid và thêm dòng sau:

  ```bash
  acl whitelist url_regex "/etc/squid/whitelist.txt"
  http_access deny !whitelist
  ```

  * Lưu ý: Cấm tất cả các trang web khác ngoại trừ trang trong whitelist.
* Xóa `www.facebook.com` ra khỏi blacklist.txt và khởi động lại Squid:

  ```
  sudo systemctl restart squid
  ```
* Kiểm tra truy cập vào `www.facebook.com` và các trang khác như `youtube.com`, `email.com`,...

---

**9. Kết thúc bài lab**

* Sau khi hoàn thành bài lab, sử dụng câu lệnh sau để kết thúc lab:

  ```
  stoplab
  ```
* Một tệp zip sẽ được tạo và lưu kết quả ở vị trí được hiển thị sau khi dùng `stoplab`.

**Lưu ý:**

* Kiểm tra các mục sau để đảm bảo bài lab đạt yêu cầu:

  * proxy\_settings
  * log\_configuration
  * black\_lists
  * white\_lists
* **Chú ý:** Đảm bảo thứ tự ACL đúng:

  ```
  http_access deny blacklist      # Đặt quy tắc cấm lên trên
  http_access allow localnet      # Quy tắc cho phép ở dưới
  http_access deny all            # Quy tắc cuối cùng để từ chối tất cả
  ```

---
