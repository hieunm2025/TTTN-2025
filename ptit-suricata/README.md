
---
**Suricata Fundamentals**

Được xem như một công cụ mạnh mẽ cho Hệ thống Phát hiện Xâm nhập (IDS), Hệ thống Ngăn chặn Xâm nhập (IPS), và Giám sát An ninh Mạng (NSM), Suricata là nền tảng quan trọng trong bảo mật mạng. Công cụ mã nguồn mở này, được quản lý và phát triển bởi Open Information Security Foundation (OISF), là minh chứng cho sức mạnh của một sáng kiến phi lợi nhuận do cộng đồng lãnh đạo.

Mục tiêu của Suricata là **phân tích tỉ mỉ từng chi tiết nhỏ nhất** trong lưu lượng mạng, tìm kiếm các dấu hiệu có thể là hành vi độc hại. Sức mạnh của nó nằm ở khả năng **đánh giá toàn diện tình trạng mạng** và **đi sâu vào chi tiết của từng giao dịch lớp ứng dụng**. Chìa khóa để Suricata hoạt động thành công là bộ quy tắc được thiết kế tinh vi. Những hướng dẫn này định hướng quá trình phân tích của Suricata, giúp nhận diện các mối đe dọa tiềm ẩn và các khu vực cần chú ý. Được trang bị khả năng **hoạt động với tốc độ cao** trên cả phần cứng sẵn có và phần cứng thiết kế riêng biệt, hiệu quả của Suricata là vô song.

---

**Suricata Operation Modes**

Suricata hoạt động trong bốn chế độ riêng biệt:

1. **Chế độ IDS (Hệ thống Phát hiện Xâm nhập)** đặt Suricata ở vai trò một **quan sát viên thụ động**. Trong chế độ này, Suricata **kiểm tra tỉ mỉ** lưu lượng mạng, đánh dấu các cuộc tấn công tiềm tàng nhưng không can thiệp. Bằng cách cung cấp cái nhìn sâu sắc về hoạt động của mạng và tăng tốc thời gian phản hồi, chế độ này nâng cao khả năng quan sát mạng mà không trực tiếp bảo vệ.

2. **Chế độ IPS (Hệ thống Ngăn chặn Xâm nhập)**, Suricata áp dụng **tư thế chủ động**. Tất cả lưu lượng mạng phải qua các kiểm tra nghiêm ngặt của Suricata và chỉ được phép truy cập vào mạng nội bộ sau khi Suricata phê duyệt. Chế độ này tăng cường bảo mật bằng cách ngăn chặn các cuộc tấn công trước khi chúng xâm nhập vào mạng nội bộ. Việc triển khai Suricata trong chế độ IPS yêu cầu hiểu rõ về cấu trúc mạng để tránh vô tình chặn các lưu lượng hợp pháp. Thêm vào đó, mỗi lần kích hoạt quy tắc cần phải được thử nghiệm và xác nhận kỹ lưỡng. Mặc dù chế độ này nâng cao bảo mật, nhưng quá trình kiểm tra có thể gây độ trễ.

3. **Chế độ IDPS (Hệ thống Phát hiện và Ngăn chặn Xâm nhập)** kết hợp những ưu điểm của cả IDS và IPS. Trong khi Suricata tiếp tục giám sát lưu lượng thụ động, nó có khả năng gửi gói tin RST để phản hồi lại các hành vi bất thường. Chế độ này đạt được sự cân bằng giữa bảo vệ chủ động và duy trì độ trễ thấp, rất quan trọng cho hoạt động mạng không bị gián đoạn.

4. Trong chế độ **NSM (Giám sát An ninh Mạng)**, Suricata trở thành một cơ chế **ghi log chuyên dụng**, bỏ qua khả năng phân tích lưu lượng chủ động hoặc thụ động hay ngăn chặn. Nó **ghi lại tỉ mỉ** mọi thông tin mạng mà nó gặp phải, cung cấp một kho dữ liệu quý giá cho các cuộc điều tra sự cố an ninh sau này, mặc dù lượng dữ liệu tạo ra rất lớn.

---

**Suricata Inputs**

Về phần đầu vào của Suricata, có hai loại chính:

* **Đầu vào ngoại tuyến**: Liên quan đến việc đọc các tệp PCAP để xử lý các gói tin đã được bắt trước đó trong định dạng tệp LibPCAP. Điều này không chỉ có lợi cho việc kiểm tra dữ liệu sau sự cố mà còn hữu ích khi thử nghiệm các bộ quy tắc và cấu hình khác nhau.

* **Đầu vào trực tiếp (Live Input)**: Đầu vào trực tiếp có thể được thực hiện qua LibPCAP, nơi các gói tin được đọc trực tiếp từ các giao diện mạng. Tuy nhiên, LibPCAP có một số hạn chế về hiệu suất và không hỗ trợ khả năng cân bằng tải. Để hoạt động trực tiếp, có thể sử dụng các tùy chọn NFQ và AF\_PACKET. NFQ, một chế độ IPS trực tiếp dành riêng cho Linux, hợp tác với IPTables để chuyển các gói tin từ không gian kernel vào Suricata để phân tích chi tiết. AF\_PACKET cung cấp cải tiến hiệu suất so với LibPCAP và hỗ trợ đa luồng. Tuy nhiên, nó không tương thích với các bản phân phối Linux cũ và không thể sử dụng khi máy chủ cũng phải định tuyến gói tin.

---

**Suricata Outputs**

Suricata tạo ra nhiều loại đầu ra khác nhau, bao gồm các bản ghi, cảnh báo, và dữ liệu mạng liên quan như yêu cầu DNS và luồng mạng. Một trong những đầu ra quan trọng nhất là **EVE**, một bản ghi theo định dạng JSON ghi lại nhiều loại sự kiện khác nhau, bao gồm cảnh báo, HTTP, DNS, TLS metadata, drop, SMTP metadata, luồng, netflow và nhiều hơn nữa. Các công cụ như Logstash có thể dễ dàng tiêu thụ đầu ra này để phục vụ việc phân tích dữ liệu.

Chúng ta cũng có thể gặp đầu ra **Unified2** của Suricata, thực tế là một định dạng cảnh báo nhị phân của Snort, cho phép tích hợp với các phần mềm khác sử dụng Unified2. Mọi đầu ra Unified2 có thể được đọc bằng công cụ u2spewfoo của Snort, một phương pháp đơn giản và hiệu quả để hiểu rõ về dữ liệu cảnh báo.

---

### Cấu hình Suricata và Quy tắc Tùy chỉnh

Sau khi truy cập vào phiên bản Suricata đã triển khai qua SSH, ta có thể kiểm tra tổng quan về tất cả các tệp quy tắc bằng lệnh đơn giản.

```bash
hieunm@htb[/htb]$ ls -lah /etc/suricata/rules/
```

Quy tắc có thể được kiểm tra dưới dạng danh sách dễ dàng và được phân tích để hiểu rõ về chức năng của chúng.

---

**Tương tác trực tiếp với Suricata Inputs và Outputs**

Để kiểm tra Suricata trong chế độ ngoại tuyến (Offline Input), ta có thể sử dụng tệp PCAP (ví dụ: suspicious.pcap) bằng lệnh sau:

```bash
hieunm@htb[/htb]$ suricata -r /home/htb-student/pcaps/suspicious.pcap
```

Tương tự, để sử dụng đầu vào trực tiếp (Live Input), ta có thể thử Suricata trong chế độ LibPCAP hoặc AF\_PACKET.

---

**Chế độ Cập nhật Quy tắc và Tải Lại Quy tắc Trực Tiếp**

Chế độ tải lại quy tắc trực tiếp trong Suricata cho phép ta cập nhật bộ quy tắc mà không làm gián đoạn quá trình kiểm tra lưu lượng mạng. Điều này rất quan trọng để duy trì giám sát liên tục và giảm thiểu khả năng bỏ sót bất kỳ hoạt động độc hại nào.

```bash
hieunm@htb[/htb]$ sudo suricata -T -c /etc/suricata/suricata.yaml
```

Với lệnh trên, ta có thể kiểm tra cấu hình của Suricata và đảm bảo rằng mọi tệp cấu hình được tham chiếu đều có thể truy cập và hợp lệ.

---

**Tóm tắt về các tính năng quan trọng của Suricata**

* Phân tích sâu gói tin và ghi log gói tin
* Phát hiện bất thường và Giám sát An ninh Mạng
* Phát hiện và Ngăn chặn Xâm nhập, với chế độ kết hợp
* Kịch bản Lua
* Xác định vị trí địa lý IP (GeoIP)
* Hỗ trợ IPv4 và IPv6 đầy đủ
* Lý lịch IP
* Trích xuất tệp
* Kiểm tra giao thức nâng cao
* Đa người dùng
* Phát hiện lưu lượng "phi chuẩn/bất thường"

---
# Suricata rules

## Quy tắc trong Suricata để giám sát và phát hiện hoạt động bất thường trong lưu lượng mạng

### 1. Cấu trúc cơ bản của quy tắc Suricata

Quy tắc trong Suricata gồm hai phần chính: **Tiêu đề** và **Thân quy tắc**.

#### - Tiêu đề (Header)

Đây là phần quyết định hành động mà Suricata sẽ thực hiện khi phát hiện dấu hiệu trong lưu lượng mạng. Nó bao gồm các thông tin như hành động (alert, log, pass, drop), giao thức (như tcp, udp, icmp), địa chỉ IP nguồn và đích, cùng cổng.

**Ví dụ**:

```text
alert tcp $HOME_NET any -> $EXTERNAL_NET 9443 (msg:"Possible attack detected"; sid:1000001;)
```

Trong ví dụ trên:

* `alert`: Hành động mà Suricata sẽ thực hiện khi điều kiện trong thân quy tắc được thỏa mãn.
* `tcp`: Giao thức mà quy tắc này áp dụng.
* `$HOME_NET any -> $EXTERNAL_NET 9443`: Quy định về hướng và cổng của lưu lượng mạng mà quy tắc này giám sát.

#### - Thân quy tắc (Body)

Phần này mô tả các điều kiện cụ thể mà Suricata sẽ tìm kiếm trong lưu lượng mạng, bao gồm các từ khóa như `content`, `msg`, `flow`, và `pcre`.

**Ví dụ**:

```text
content:"malicious_string"; msg:"Malicious content detected"; sid:1000001;
```

* `content:"malicious_string"`: Tìm kiếm chuỗi `"malicious_string"` trong nội dung gói tin.
* `msg:"Malicious content detected"`: Hiển thị thông báo khi quy tắc bị kích hoạt.

---

### 2. Các phần trong quy tắc Suricata

* **action**: Xác định hành động mà Suricata sẽ thực hiện, ví dụ `alert` (cảnh báo), `log` (nhật ký), `pass` (bỏ qua gói tin), `drop` (loại bỏ gói tin), `reject` (từ chối kết nối).
* **protocol**: Giao thức mạng mà quy tắc áp dụng, ví dụ `tcp`, `udp`, `http`, `dns`.
* **IP và cổng**: Quy định địa chỉ IP và cổng nguồn và đích của lưu lượng mà quy tắc giám sát.

  * Ví dụ: `$HOME_NET any -> $EXTERNAL_NET 80` (giám sát lưu lượng HTTP từ mạng nội bộ ra bên ngoài).
* **msg**: Tin nhắn hiển thị khi quy tắc được kích hoạt, mô tả về sự kiện hoặc hành động đáng ngờ.
* **content**: Đoạn nội dung cụ thể mà Suricata sẽ tìm kiếm trong gói tin, có thể là chuỗi ký tự hoặc giá trị byte cụ thể trong dữ liệu gói tin.
* **flow**: Xác định hướng của lưu lượng, ví dụ `to_server` (đến máy chủ) hoặc `from_client` (từ máy khách).
* **sid (Signature ID)**: ID duy nhất để nhận diện quy tắc, giúp phân biệt và quản lý quy tắc.

---

### 3. Ví dụ về quy tắc Suricata

```text
alert tcp any any -> $HOME_NET 80 (msg:"Possible HTTP attack"; content:"malicious"; sid:1000001;)
```

Quy tắc này sẽ phát hiện bất kỳ lưu lượng HTTP nào đến từ bất kỳ IP và cổng nào vào máy chủ nội bộ (`$HOME_NET`) trên cổng 80. Nếu phát hiện chuỗi `"malicious"` trong nội dung, quy tắc sẽ kích hoạt một cảnh báo.

---

### 4. Sử dụng Regex (PCRE) trong Suricata

Quy tắc Suricata cũng hỗ trợ sử dụng **biểu thức chính quy (PCRE)** để phát hiện các mẫu phức tạp trong nội dung gói tin.

**Ví dụ**:

```text
pcre:"/\/(php|api|upload|actions)\.php\?/i";
```

Biểu thức trên sẽ phát hiện các yêu cầu HTTP chứa chuỗi `/php`, `/api`, `/upload`, hoặc `/actions` kết thúc bằng `.php?`.

---

### Quy trình phát triển quy tắc Suricata

1. **Thu thập thông tin và dữ liệu**: Để tạo quy tắc hiệu quả, cần thu thập dữ liệu về các mối đe dọa hoặc hành vi mạng có thể xảy ra. Dữ liệu này có thể đến từ các cộng đồng an ninh mạng hoặc các nguồn thông tin về mối đe dọa (threat intelligence).

2. **Viết quy tắc**: Dựa trên thông tin thu thập được, ta sẽ xây dựng các quy tắc với các điều kiện và hành động cụ thể tương ứng.

3. **Kiểm tra và tối ưu hóa**: Sau khi viết xong, cần đảm bảo quy tắc bằng cách sử dụng các gói tin pcap và điều chỉnh quy tắc sao cho tránh được cảnh báo sai (false positives).

4. **Triển khai và giám sát**: Triển khai quy tắc vào hệ thống Suricata và theo dõi hiệu suất cùng các cảnh báo.

---

### SSL/TLS Certificates và Dấu Vân Tay JA3

Trong bối cảnh an ninh mạng ngày nay, một trong những thách thức lớn là phân tích lưu lượng mã hóa. Lưu lượng mã hóa có thể gây khó khăn khi xây dựng các quy tắc phát hiện xâm nhập (IDS) và ngăn chặn xâm nhập (IPS) hiệu quả. Tuy nhiên, chúng ta có thể khai thác các yếu tố như chứng chỉ SSL/TLS và dấu vân tay JA3 để phát hiện các mối đe dọa.

#### **SSL/TLS Certificates**

Các chứng chỉ SSL/TLS trao đổi trong quá trình bắt tay của một kết nối SSL/TLS chứa nhiều chi tiết chưa được mã hóa. Những chi tiết này bao gồm thông tin như nhà phát hành, ngày cấp, ngày hết hạn, đối tượng (chứa thông tin về chủ sở hữu của chứng chỉ và tên miền). Các tên miền độc hại có thể sử dụng chứng chỉ này với các đặc điểm bất thường hoặc duy nhất, giúp nhận diện các hành vi đáng ngờ.

#### **JA3 Hash**

**JA3** là một phương pháp dấu vân tay cung cấp một đại diện duy nhất cho mỗi máy khách SSL/TLS. JA3 hash kết hợp các chi tiết từ các gói client hello trong quá trình bắt tay SSL/TLS, tạo ra một digest có thể là duy nhất cho các gia đình malware.

---

### Ví dụ về Phát hiện Dridex qua SSL/TLS Mã hóa

```text
alert tls $EXTERNAL_NET any -> $HOME_NET any (msg:"ET MALWARE ABUSE.CH SSL Blacklist Malicious SSL certificate detected (Dridex)"; flow:established,from_server; content:"|16|"; content:"|0b|"; within:8; byte_test:3,<,1200,0,relative; content:"|03 02 01 02 02 09 00|"; fast_pattern; content:"|30 09 06 03 55 04 06 13 02|"; distance:0; pcre:"/^[A-Z]{2}/R"; content:"|55 04 07|"; distance:0; content:"|55 04 0a|"; distance:0; pcre:"/^.{2}[A-Z][a-z]{3,}\s(?:[A-Z][a-z]{3,}\s)?(?:[A-Z](?:[A-Za-z]{0,4}?[A-Z]|(?:\.[A-Za-z]){1,3})|[A-Z]?[a-z]+|[a-z](?:\.[A-Za-z]){1,3})\.?[01]/Rs"; content:"|55 04 03|"; distance:0; byte_test:1,>,13,1,relative; content:!"www."; distance:2; within:4; pcre:"/^.{2}(?P<CN>(?:(?:\d?[A-Z]?|[A-Z]?\d?)(?:[a-z]{3,20}|[a-z]{3,6}[0-9_][a-z]{3,6})\.){0,2}?(?:\d?[A-Z]?|[A-Z]?\d?)[a-z]{3,}(?:[0-9_-][a-z]{3,})?\.(?!com|org|net|tv)[a-z]{2,9})[01].*?(?P=CN)[01]/Rs"; content:!"|2a 86 48 86 f7 0d 01 09 01|"; content:!"GoDaddy"; sid:2023476; rev:5;)
```

---

### Ví dụ về Phát hiện qua JA3 Hash

```text
alert tls any any -> any any (msg:"Sliver C2 SSL"; ja3.hash; content:"473cd7cb9faa642487833865d516e578"; sid:1002; rev:1;)
```

Quy tắc này giúp phát hiện lưu lượng mã hóa TLS mà phần mềm **Sliver** tạo ra trong các cuộc tấn công C2 (Command and Control).

---
