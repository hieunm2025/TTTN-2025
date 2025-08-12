### Hướng Dẫn Toàn Diện Về Snort


#### 1. Giới Thiệu Về Snort
Snort là một công cụ mã nguồn mở mạnh mẽ, được sử dụng rộng rãi trong lĩnh vực an ninh mạng. Nó hoạt động như một Hệ thống Phát hiện Xâm nhập (IDS) và Hệ thống Ngăn chặn Xâm nhập (IPS), đồng thời có thể đóng vai trò như công cụ ghi lại gói tin hoặc sniff lưu lượng mạng (tương tự Suricata). Với khả năng phân tích toàn bộ lưu lượng mạng, Snort giúp phát hiện và ghi nhận các hoạt động đáng ngờ, mang lại cái nhìn tổng quan cho quản trị viên về tình trạng mạng và các giao dịch ứng dụng. Snort được thiết kế để chạy hiệu quả trên cả phần cứng thông thường lẫn tùy chỉnh, và yêu cầu các bộ quy tắc cụ thể để hướng dẫn quá trình kiểm tra và phát hiện mối đe dọa.

Snort hỗ trợ kiểm tra gói tin sâu, capture và log gói tin, phát hiện xâm nhập, giám sát an ninh mạng, phát hiện bất thường, hỗ trợ đa tenant, cũng như cả IPv6 và IPv4.

#### 2. Các Chế Độ Hoạt Động Của Snort
Snort linh hoạt với nhiều chế độ hoạt động, phù hợp với nhu cầu khác nhau:
- **Inline IDS/IPS**: Chế độ này cho phép Snort không chỉ phát hiện mà còn ngăn chặn lưu lượng mạng nếu phát hiện gói tin vi phạm quy tắc, hoạt động như một "lá chắn" chủ động.
- **Passive IDS**: Ở chế độ thụ động, Snort chỉ quan sát và ghi nhận các sự kiện đáng ngờ mà không can thiệp, giúp theo dõi mà không ảnh hưởng đến hiệu suất mạng.
- **Network-based IDS**: Tập trung giám sát lưu lượng trên toàn mạng, lý tưởng cho việc phát hiện các cuộc tấn công từ xa.
- **Host-based IDS**: Mặc dù Snort có thể áp dụng cho máy chủ cá nhân, nhưng nó không phải là công cụ tối ưu nhất cho loại hình này, vì các giải pháp chuyên biệt khác có thể hiệu quả hơn.

Theo tài liệu Snort, với các module DAQ (Data Acquisition Library), Snort có thể hoạt động ở chế độ passive (quan sát và phát hiện mà không chặn) hoặc inline (cho phép chặn lưu lượng). Chế độ được suy ra từ tùy chọn dòng lệnh, ví dụ -r cho passive khi đọc file pcap, hoặc -Q cho inline nếu DAQ hỗ trợ (như afpacket trên Linux).

#### 3. Kiến Trúc Của Snort
Snort được xây dựng với kiến trúc modul hóa, bao gồm các thành phần chính: **Packet Sniffer (kèm Packet Decoder)**, **Preprocessor**, **Detection Engine**, **Logging and Alerting System**, và **Output Modules**. Quy trình hoạt động diễn ra mượt mà như sau:
- **Packet Sniffer**: Thu thập và phân tích cấu trúc của các gói tin mạng, sau đó chuyển tiếp chúng đến các giai đoạn xử lý tiếp theo.
- **Preprocessor**: Nhận dạng loại gói tin, phát hiện các hành vi bất thường như quét cổng hoặc tấn công từ chối dịch vụ. Các plugin như HTTP (phân biệt gói HTTP) hoặc port_scan (phát hiện quét cổng dựa trên giao thức và ngưỡng) được cấu hình trong file snort.lua.
- **Detection Engine**: Là "trái tim" của Snort, so sánh gói tin với các quy tắc định sẵn để xác định các cuộc tấn công.
- **Logging and Alerting System**: Ghi nhận kết quả và tạo cảnh báo, giúp quản trị viên phản ứng kịp thời. Log thường lưu dưới dạng syslog, unified2 hoặc cơ sở dữ liệu.

#### 4. Cấu Hình Snort Và Xác Thực Cấu Hình
Việc cấu hình Snort chủ yếu dựa vào file **snort.lua**, nơi bạn có thể thiết lập các biến mạng (như HOME_NET và EXTERNAL_NET), trình giải mã gói tin, engine phát hiện, preprocessors, và module output. File này bao gồm các phần như network variables, decoder configuration, base detection engine, dynamic library, preprocessor, output plugin, và tùy chỉnh quy tắc. Quá trình cấu hình khá đơn giản nhờ các module sẵn có; bạn chỉ cần chỉnh sửa file hoặc thêm module mới.

Để liệt kê module: `snort --help-modules`. Để xem mặc định: `snort --help-config <module_name>` (ví dụ: arp_spoof).

Để kiểm tra tính hợp lệ của cấu hình: `snort -c /root/snorty/etc/snort/snort.lua --daq-dir /usr/local/lib/daq`. DAQ là thư viện trừu tượng hóa để giao tiếp với nguồn dữ liệu mạng, không bắt buộc cho validation nhưng hữu ích cho replication.

Khuyến nghị đọc comment trong snort.lua để hiểu chi tiết.

#### 5. Snort Inputs Và Test Rules
Snort hỗ trợ phân tích dữ liệu đầu vào đa dạng:
- Chạy trên file pcap (gói tin đã ghi sẵn) bằng lệnh `snort -r <pcap_file>` để kiểm tra mối đe dọa, ví dụ: `sudo snort -c /root/snorty/etc/snort/snort.lua --daq-dir /usr/local/lib/daq -r /home/htb-student/pcaps/icmp.pcap`.
- Nghe trên giao diện mạng: `sudo snort -c ... -i <interface>` (ví dụ: ens160), dừng bằng Ctrl+C.

Khi test quy tắc tùy chỉnh, sử dụng -R cho file quy tắc đơn lẻ hoặc --rule-path cho thư mục. Quy tắc có thể đặt trực tiếp trong snort.lua qua module ips, hoặc tải từ Snort/Emerging Threats.

#### 6. Cấu Hình Output Của Snort
Snort cung cấp nhiều loại output để hỗ trợ phân tích:
- **Thống Kê Cơ Bản**: Bao gồm Packet Statistics (số gói nhận, phân tích), Module Statistics (peg counts như HTTP GET), File Statistics (loại file, byte), Summary Statistics (thời gian xử lý, gói/giây).
- **Cảnh Báo (Alerts)**: Bật bằng -A, ví dụ -A cmg (hiển thị alert với header/payload), -A u2 (binary), -A csv (comma-separated). Xem loại: `snort --list-plugins | grep logger`.
- **Thống Kê Hiệu Năng**: Sử dụng perf_monitor để theo dõi peg counts thời gian thực, profiler để đo thời gian/bộ nhớ (hiển thị dưới Summary Statistics).

Ví dụ output cmg trên icmp.pcap sẽ hiển thị alert chi tiết cho gói ICMP khớp quy tắc, kèm payload hex.

#### 7. Viết Và Phát Triển Quy Tắc Snort
Quy tắc Snort được thiết kế đơn giản nhưng mạnh mẽ, gồm hai phần chính: **Header** (xác định giao thức và địa chỉ mạng) và **Options** (các điều kiện chi tiết như `content` để tìm chuỗi, `flow` để theo dõi dòng dữ liệu, hoặc `sid` để gán ID quy tắc). Quy tắc Snort giống Suricata nhưng có khác biệt; học từ [docs.snort.org](https://docs.snort.org/) và [docs.suricata.io/en/latest/rules/differences-from-snort.html](https://docs.suricata.io/en/latest/rules/differences-from-snort.html).

Dưới đây là các ví dụ thực tế để phát hiện malware (quy tắc đã có trong local.rules tại /home/htb-student; uncomment để test trên file pcap tương ứng):

- **Ví Dụ 1: Phát Hiện Ursnif (Không Hiệu Quả)**:  
  ```
  alert tcp any any -> any any (msg:"Possible Ursnif C2 Activity"; flow:established,to_server; content:"/images/", depth 12; content:"_2F"; content:"_2B"; content:"User-Agent|3a 20|Mozilla/4.0 (compatible|3b| MSIE 8.0|3b| Windows NT"; content:!"Accept"; content:!"Cookie|3a|"; content:!"Referer|3a|"; sid:1000002; rev:1;)
  ```
  Test trên ursnif.pcap: `sudo snort -c ... -R local.rules -r ursnif.pcap -A cmg`. Phân tích bằng Wireshark để hiểu.

- **Ví Dụ 2: Phát Hiện Cerber**:  
  ```
  alert udp $HOME_NET any -> $EXTERNAL_NET any (msg:"Possible Cerber Check-in"; dsize:9; content:"hi", depth 2, fast_pattern; pcre:"/^[af0-9]{7}$/R"; detection_filter:track by_src, count 1, seconds 60; sid:2816763; rev:4;)
  ```
  Test trên cerber.pcap.

- **Ví Dụ 3: Phát Hiện Patchwork**:  
  ```
  alert http $HOME_NET any -> $EXTERNAL_NET any (msg:"OISF TROJAN Targeted AutoIt FileStealer/Downloader CnC Beacon"; flow:established,to_server; http_method; content:"POST"; http_uri; content:".php?profile="; http_client_body; content:"ddager=", depth 7; http_client_body; content:"&r1=", distance 0; http_header; content:!"Accept"; http_header; content:!"Referer|3a|"; sid:10000006; rev:1;)
  ```
  Test trên patchwork.pcap.

- **Ví Dụ 4: Phát Hiện Patchwork (Qua SSL)**:  
  ```
  alert tcp $EXTERNAL_NET any -> $HOME_NET any (msg:"Patchwork SSL Cert Detected"; flow:established,from_server; content:"|55 04 03|"; content:"|08|toigetgf", distance 1, within 9; classtype:trojan-activity; sid:10000008; rev:1;)
  ```
  Test trên patchwork.pcap.

Tải file pcap bằng scp từ máy target. Việc phát triển quy tắc đòi hỏi hiểu lưu lượng và tối ưu để tránh false positive.

#### 8. Đo Lường Hiệu Năng Và Thống Kê
Snort cung cấp các chỉ số hiệu suất như tốc độ xử lý gói tin (packets/sec), sử dụng bộ nhớ (memory usage), và bộ nhớ mẫu (pattern memory). Các module như **perf_monitor** và **profiler** cho phép theo dõi liên tục mà không làm gián đoạn hoạt động, giúp quản trị viên điều chỉnh cấu hình để đạt hiệu suất cao nhất.

Tóm lại, Snort là công cụ linh hoạt và mạnh mẽ cho an ninh mạng, với khả năng tùy chỉnh cao. Việc nắm vững lý thuyết và thực hành trên máy target (qua SSH) sẽ giúp triển khai hiệu quả. Nếu cần mở rộng hoặc chỉnh sửa, hãy cho tôi biết!