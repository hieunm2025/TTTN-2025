
---

**Nội dung và hướng dẫn thực hiện bài thực hành**
Vào terminal nhập imodule để cài đặt bài lab về:

imodule https://github.com/hieunm2025/TTTN-2025/raw/refs/heads/main/ptit-snort-detect/imodule.tar

**Mục đích:**

* Giúp sinh viên hiểu về công cụ Snort và cách sử dụng nó trong bảo mật mạng.
* Nâng cao kỹ năng phân tích và giải quyết các vấn đề bảo mật thực tế.
* Tìm hiểu về các công cụ như Nmap, Crunch, Snort, Hydra, và Hashcat.
* Tăng cường ý thức về trách nhiệm và đạo đức trong bảo mật mạng.

**Yêu cầu đối với sinh viên:**

* Có kiến thức cơ bản về hệ điều hành Linux, mô hình mạng khách/chủ.
* Biết sử dụng các công cụ cơ bản như Nmap, Hydra, Snort, và Hashcat.

**Nội dung thực hành:**

1. **Khởi động bài lab:**

   * Mở terminal và nhập lệnh sau:

     ```
     labtainer -r ptit-snort-detect
     ```


2. **Khởi tạo môi trường thực hành:**

   * Sau khi khởi động xong, ba terminal ảo sẽ xuất hiện

3. **Quét cổng mở trên máy server bằng Nmap:**

   * Sử dụng lệnh sau để quét cổng mở:

     ```
     sudo nmap 172.25.0.3 -sS
     ```

4. **Tạo bộ từ điển cho tài khoản và mật khẩu:**

   * **Khuyến nghị thay đổi:** Do Crunch có thể gây thiếu bộ nhớ khi tạo bộ từ điển quá lớn, sinh viên được khuyến khích sử dụng **Hashcat** để tấn công mật khẩu hiệu quả hơn.

     * Dùng lệnh sau để crack mật khẩu:

       ```
       hashcat -m 1800 hash.txt /usr/share/wordlists/rockyou.txt
       ```


5. **Tạo và thêm luật Snort để phát hiện các cuộc tấn công brute force:**

   * Tạo file luật trong thư mục `/etc/snort/rules/` với tên luật tùy chỉnh.

     ```
     vi /etc/snort/rules/<tentapluat>
     ```

6. **Tấn công brute force bằng Hydra:**

   * Sử dụng lệnh Hydra để tấn công SSH:

     ```
     hydra -L <tên file tài khoản> -P <tên file mật khẩu> 172.25.0.3 ssh
     ```

7. **SSH vào máy server sau khi brute force thành công:**

   * Sử dụng lệnh sau để SSH vào server:

     ```
     ssh <username>@172.25.0.3
     ```

8. **Đọc nội dung file sau khi SSH thành công:**

   * Sử dụng lệnh:

     ```
     cat /root/filetoview.txt
     ```

9. **Cấu hình iptables trên máy firewall để chặn cổng 22 (SSH):**

   * Thực hiện chặn cổng 22:

     ```
     sudo iptables -A INPUT -p tcp --dport 22 -j REJECT
     ```

10. **Tạo và thêm luật Snort để phát hiện cuộc tấn công XSS:**

    * Tạo và thêm luật Snort tại `/etc/snort/rules/` để phát hiện tấn công XSS.

11. **Tạo và thêm luật Snort để phát hiện SQL Injection:**

    * Tạo và thêm luật Snort tại `/etc/snort/rules/` để phát hiện tấn công SQL Injection.

12. **Tiến hành tấn công SQL Injection và XSS:**

    * Truy cập website tại `http://172.25.0.3/dvwa` và thực hiện tấn công SQL Injection và XSS.
    * Snort sẽ phát hiện và đưa ra cảnh báo khi có sự tấn công.



    * Link tải tài nguyên bài lab (ptit-snort-detect):

      ```
      https://github.com/Quandna/ptit-snort-detect/releases/download/ptit-snort-attack/imodule.tar
      ```

