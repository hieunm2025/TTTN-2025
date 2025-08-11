## Bài Thực Hành: Cấu Hình và Sử Dụng Suricata, Hydra, Nmap, và Hping3

### Mục Đích

* Hiểu các kiểu quét (scan) trong Nmap.
* Sử dụng công cụ Hydra để tấn công vết cạn (brute-force).
* Sử dụng công cụ Hping3 để thực hiện tấn công từ chối dịch vụ (DoS).
* Thực hành cấu hình và viết rule trong Suricata.

### Yêu Cầu Đối Với Sinh Viên

* Có kiến thức về hệ điều hành Linux.
* Cài đặt và sử dụng các công cụ cơ bản như Nmap, Hydra, Hping3, Suricata.

### Nội Dung Thực Hành

1. **Tải Lab**
   Sinh viên tải lab tại:
   [imodule.tar](https://github.com/nguyenductuan12/ptit-suricata/raw/main/imodule.tar)

2. **Cập Nhật Thời Gian Trên Máy Ubuntu**
   Để cập nhật thời gian thực trên máy Ubuntu:

   ```bash
   sudo apt install ntpdate
   sudo ntpdate time.windows.com
   ```

3. **Khởi Động Lab**
   Mở terminal và chạy lệnh sau để khởi động lab:

   ```bash
   labtainer -r ptit-suricata
   ```

   * Hai terminal ảo sẽ xuất hiện:

     * **server**: Đại diện cho máy server.
     * **attacker**: Đại diện cho máy attacker.
     * Cả hai máy nằm trong cùng một dải mạng và máy attacker đã biết địa chỉ IP của máy server.

4. **Cấu Hình Suricata Trên Máy Server**
   Trên máy **server**, thực hiện cấu hình Suricata qua tệp cấu hình `/etc/suricata/suricata.yaml`.

   * Cấu hình dải mạng cần giám sát:

     * Do hai máy nằm cùng dải mạng, cấu hình trường `HOME_NET`.
   * Cấu hình đường dẫn đến tệp quy tắc Suricata trong trường `rule-files`.
   * Khởi động lại dịch vụ Suricata để cập nhật cấu hình:

     ```bash
     sudo systemctl restart suricata
     ```

5. **Cập Nhật Các Tệp Luật Suricata**
   Cập nhật các tập luật có sẵn và tệp luật tự định nghĩa:

   ```bash
   suricata-update
   ```

6. **Tạo File Định Nghĩa Các Rule Riêng**
   Tạo một tệp luật tự định nghĩa trong thư mục đã cấu hình trong `suricata.yaml`.

   Tham khảo hướng dẫn sử dụng Suricata rules tại: [Suricata Rule Documentation](https://docs.suricata.io/en/latest/rules/index.html)

7. **Viết Rule Phát Hiện Nmap Scan**

   * Viết rule phát hiện quét Nmap TCP (sử dụng lệnh `nmap -sT`) với thông điệp cảnh báo:

     ```bash
     alert tcp $HOME_NET any -> $HOME_NET any (msg:"POSSBL SCAN NMAP KNOWN TCP (type -sT) - {MSV}"; flow:stateless; classtype: attempted-recon; sid:1000001; threshold:type limit, track by_src, count 10, seconds 300; dsize:0;)
     ```
   * Trên máy **attacker**, thực hiện quét cổng TCP:

     ```bash
     nmap -sT <IP_victim>
     ```

8. **Viết Rule Phát Hiện Nmap Scan UDP**

   * Viết rule phát hiện quét Nmap UDP (sử dụng lệnh `nmap -sU`) với thông điệp cảnh báo:

     ```bash
     alert udp $HOME_NET any -> $HOME_NET any (msg:"POSSBL SCAN NMAP KNOWN UDP (type -sU) - {MSV}"; flow:stateless; classtype: attempted-recon; sid:1000002; threshold:type limit, track by_src, count 10, seconds 300; dsize:0;)
     ```
   * Trên máy **attacker**, thực hiện quét cổng UDP:

     ```bash
     nmap -sUV -F -T5 <IP_victim>
     ```

9. **Viết Rule Phát Hiện Tấn Công ICMP Flood**

   * Viết rule phát hiện tấn công ICMP flood với thông điệp cảnh báo:

     ```bash
     alert icmp $HOME_NET any -> $HOME_NET any (msg:"POSSBL DOS ICMP PACKET FLOOD - {MSV}"; sid:1000003;)
     ```
   * Trên máy **attacker**, sử dụng công cụ Hping3 để tấn công DoS vào máy victim:

     ```bash
     sudo hping3 -1 --flood <IP_victim>
     ```

10. **Viết Rule Phát Hiện Tấn Công Brute-Force SSH**

    * Viết rule phát hiện tấn công brute-force vào dịch vụ SSH với thông điệp cảnh báo:

      ```bash
      alert tcp $HOME_NET any -> $HOME_NET 22 (msg:"POSSBL SSH BRUTE FORCING! - {MSV}"; classtype: attempted-admin; sid:1000004;)
      ```
    * Trên máy **attacker**, sử dụng công cụ Hydra để thực hiện tấn công brute-force vào SSH:

      ```bash
      hydra -L username.txt -P passlist.txt ssh://<IP_victim>
      ```

11. **Tải File Từ Victim Về Máy Attacker**

    * Sau khi thành công trong việc brute-force, tải file từ victim về và đọc nó trên máy attacker.

12. **Kết Thúc Bài Lab**

    * Kiểm tra kết quả thực hiện bài lab bằng cách sử dụng lệnh `checkwork`:

      ```bash
      checkwork
      ```
    * Để đóng bài lab, sử dụng lệnh `stoplab`:

      ```bash
      stoplab
      ```
    * Sau khi dừng bài lab, một tệp zip chứa kết quả sẽ được tạo và lưu ở vị trí hiển thị.

13. **Khởi Động Lại Lab**

    * Trong trường hợp cần làm lại bài lab, dùng lệnh:

      ```bash
      startlab -r lab_suricata
      ```



* `{MSV}` trong các thông điệp cảnh báo là mã sinh viên của bạn. Thay thế `{MSV}` bằng mã sinh viên của bạn trong các rule.
* Hãy kiểm tra các log của Suricata trong thư mục `/var/log/suricata/` để theo dõi cảnh báo và phát hiện các tấn công.
