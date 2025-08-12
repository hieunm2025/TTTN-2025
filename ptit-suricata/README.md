# suricata

Quy tắc trong suricata để giám sát và phát hiện hoạt động bất thường trong lưu lượng mạng
Cấu trúc quy tắc trong suricata:
1. Cấu trúc cơ bản
Quy tắc trong Suricata gồm 2 phần chính: Tiêu đề và Thân quy tắc
- Tiêu đề (Header): Đây là phần quyết định hành động mà Suricata sẽ thực hiện khi phát hiện dấu hiệu trong lưu lượng mạng. Nó bao gồm các thông tin như hành động(alert, log, pass, drop), giao thức (như tco,udp,icmp), địa chỉ IP nguồn và đích, cũng như cổng.
**Ví dụ**:
```text
alert tcp $HOME_NET any -> $EXTERNAL_NET 9443 (msg:"Possible attack detected"; sid:1000001;)
```
Trong ví dụ trên:
+alert là hành động mà Suricata sẽ thực hiện khi điều kiện trong thân quy tắc được thỏa mãn.
+ tcp là giao thức mà quy tắc này áp dụng.
+ $HOME_NET any -> $EXTERNAL_NET 9443 là quy định về hướng và cổng của lưu lượng mạng mà quy tắc này giám sát.
- Thân quy tắc(Body): Phần này mô tả các điều kiện cụ thể mà Suricata sẽ tìm kiếm trong lưu lượng mạng, bao gồm các từ khóa như content, msg, flow và prce
Ví dụ:
```text
content:"malicious_string"; msg:"Malicious content detected"; sid:1000001;
```
content:"malicious_string": tìm kiếm chuỗi "malicious_string" trong nd gói tin
msg: "Malicious content detected": Hiển thị thông báo khi quy tắc bị kích hoạt

2. Các phần trong quy tắc:
**action**: Xác định hành động mà Suricata sẽ thực hiện, alert(cảnh báo), log(Nhật kí), pass(bỏ qua gói tin), drop(loại bỏ gói tin), reject(từ chối kết nối)
**protocol**: giao thức mạng mà quy tắc áp dụng, tcp,udp,http,dns...
**IP và cổng**: Quy định địa chỉ IP và cổng nguồn và đích của lưu lượng mà quy tắc giám sát
$HOME_NET any -> $EXTERNAL_NET 80 (giám sát lưu lượng HTTP từ mạng nội bộ ra bên ngoài)
**msg**: Tin nhắn hiển thị khi quy tắc được kích hoạt. Thông thường sẽ bao gồm mô tả về sự kiện hoặc hành động đáng ngờ
**content**: Đoạn nội dung cụ thể mà Suricata sẽ tìm kiếm trong gói tin. Đoạn này có thể là chuỗi ký tự hoặc giá trị byte cụ thể trong dữ liệu gói tin.
**flow**: Xác định hướng của lưu lượng, ví dụ to_server(đến máy chủ) hoặc from_client(từ máy khách)
**sid(Signature ID)**: ID duy nhất để nhận diện quy tắc. Mỗi quy tắc có một sid khác nhau, giúp phân biệt và quản lý quy tắc.

3. Ví dụ
```
alert tcp any any -> $HOME_NET 80 (msg:"Possible HTTP attack"; content:"malicious"; sid:1000001;)
```
Quy tắc sẽ phát hiện bất cứ lưu lượng HTTP nào đến từ bất kỳ IP và cổng nào vào máy chủ nội bộ ($HOME_NET) trên cổng 80. Nếu phát hiện chuỗi malicious trong nd, quy tắc kích hoạt một cảnh báo
4. Sử dụng Regex(PCRE)
pcre:"/\/(php|api|upload|actions)\.php\?/i";
Regex trên sẽ phát hiện các yêu cầu HTTP chứa chuỗi /php, /api, /upload hoặc /actions kết thúc bằng .php?

**Quy trình phát triển quy tắc Suricata**
1. Thu thập thông tin và dữ liệu: Để tạo quy tắc hiệu quả, cần thu thập dữ liệu về các mối đe dọa hoặc hành vi mạng có thể xảy ra. Dữ liệu này có thể đến từ các cộng đồng an ninh mạng hoặc có nguồn thông tin về mối đe dọa(threat intelligence)
2. Viết rule: Dựa trên thông tin thu thập được, ta sẽ xây dựng được các quy tắc với các điều kiện và hành động cụ thể tương ứng
3. Kiểm tra và tối ưu: Sau khi viết xong, cần đảm bảo quy tắc bằng cách sử dụng các gói tin pcap và điều chỉnh quy tắc sao cho để tránh cảnh báo sai (false positives).
4. Triển khi và giám sát: Triển khai quy tắc vào hệ thống Suricata và theo dõi hiệu suất cũng như cảnh báo
<<<<<<< HEAD

Trong bối cảnh an ninh mạng ngày nay, thách thức lớn mà chúng ta gặp phải là lưu lượng mã hóa. Lưu lượng mã hóa có thể gây ra khó khăn lớn khi phân tích và xây dựng các quy tắc phát hiện xâm nhập (IDS) và ngăn chặn xâm nhập(IPS) hiệu quả. Tuy nhiên có một số yếu tố mà chúng ta có thể khai thác như thông tin chứng chỉ SSL/TLS và dấu vân tay JA3

**SSL/TLS Certificates và Dấu vân tay JA3**
Các chứng chỉ SSL/TLS được trao đổi trong quá trình bắt tay của một kết nối SSL/TLS, chứa nhiều chi tiết vân chưa được mã hóa. Những chi tiết này có thể bao gồm thông tin về nhà phát hành, ngày cấp, ngày hết hạn, đối tượng(chứa thông tin về owner của chứng chỉ và tên miền). Các tên miền độc hại có thể dùng chứng chỉ này với các đặc điểm bất thường hoặc duy nhất. Việc nhận diện bất thường trong SSL/TLS certificate là bước đệm để xây dựng các quy tắc Suricata hiệu quả.

Ngoài ra, có thể tận dụng **JA3 Hash** một phương pháp dấu vân tay cung cấp một địa diện duy nhất cho máy khách SSL/TLS. JA3 hash kết hợp các chi tiết từ các gói client hello trong quá trình bắt tay SSL/TLS, tạo một digest có thể là duy nhất cho các họ malware. 

Ví dụ 1: Phát hiện Dirdex qua SSL/TLS mã hóa
```
alert tls $EXTERNAL_NET any -> $HOME_NET any (msg:"ET MALWARE ABUSE.CH SSL Blacklist Malicious SSL certificate detected (Dridex)"; flow:established,from_server; content:"|16|"; content:"|0b|"; within:8; byte_test:3,<,1200,0,relative; content:"|03 02 01 02 02 09 00|"; fast_pattern; content:"|30 09 06 03 55 04 06 13 02|"; distance:0; pcre:"/^[A-Z]{2}/R"; content:"|55 04 07|"; distance:0; content:"|55 04 0a|"; distance:0; pcre:"/^.{2}[A-Z][a-z]{3,}\s(?:[A-Z][a-z]{3,}\s)?(?:[A-Z](?:[A-Za-z]{0,4}?[A-Z]|(?:\.[A-Za-z]){1,3})|[A-Z]?[a-z]+|[a-z](?:\.[A-Za-z]){1,3})\.?[01]/Rs"; content:"|55 04 03|"; distance:0; byte_test:1,>,13,1,relative; content:!"www."; distance:2; within:4; pcre:"/^.{2}(?P<CN>(?:(?:\d?[A-Z]?|[A-Z]?\d?)(?:[a-z]{3,20}|[a-z]{3,6}[0-9_][a-z]{3,6})\.){0,2}?(?:\d?[A-Z]?|[A-Z]?\d?)[a-z]{3,}(?:[0-9_-][a-z]{3,})?\.(?!com|org|net|tv)[a-z]{2,9})[01].*?(?P=CN)[01]/Rs"; content:!"|2a 86 48 86 f7 0d 01 09 01|"; content:!"GoDaddy"; sid:2023476; rev:5;)
```
content:"|16|"; content:"|0b|"; within:8;: Quy tắc tìm kiếm giá trị hex 16 (biểu tượng cho thông báo bắt tay) và 0b (chứng chỉ SSL) trong 8 byte đầu tiên của gói tin.

content:"|03 02 01 02 02 09 00|"; fast_pattern;: Tìm kiếm một mẫu byte cụ thể có thể liên quan đến chứng chỉ SSL của Dridex.

content:"|30 09 06 03 55 04 06 13 02|"; distance:0; pcre:"/^[A-Z]{2}/R";: Kiểm tra trường 'countryName' trong chứng chỉ, khớp với mã quốc gia có dạng hai chữ cái viết hoa.

content:"|55 04 03|"; distance:0; byte_test:1,>,13,1,relative;: Kiểm tra trường 'commonName' trong chứng chỉ và kiểm tra độ dài của trường này có lớn hơn 13.

Quy tắc này sẽ phát hiện chứng chỉ SSL có liên quan đến phần mềm độc hại Dridex, được sử dụng trong các cuộc tấn công qua giao thức TLS.

Ví dụ 2: Phát hiện qua JA3 hash
```
alert tls any any -> any any (msg:"Sliver C2 SSL"; ja3.hash; content:"473cd7cb9faa642487833865d516e578"; sid:1002; rev:1;)
```
ja3.hash: Quy tắc này tìm kiếm các kết nối TLS có hash JA3.

content:"473cd7cb9faa642487833865d516e578";: Tìm kiếm một hash JA3 cụ thể liên quan đến phần mềm Sliver, được sử dụng để nhận diện các phiên bản C2 của nó.

Quy tắc này giúp phát hiện lưu lượng mã hóa TLS mà phần mềm Sliver tạo ra trong các cuộc tấn công C2 (Command and Control).
=======
>>>>>>> fca31008b27657d0e39901a6a085a93e4de2e980
