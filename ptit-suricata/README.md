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
