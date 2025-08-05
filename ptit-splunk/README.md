# Sửa lỗi đường dẫn tải lab và viết lại hướng dẫn
### **Nội dung và Hướng dẫn Bài Thực Hành - Cài đặt và Sử dụng Splunk**

#### **Mục đích Bài Thực Hành**

Bài thực hành này giúp sinh viên làm quen với **Splunk**, một công cụ mạnh mẽ dùng để tìm kiếm, giám sát và phân tích dữ liệu máy tính qua giao diện web. Cụ thể, sinh viên sẽ:

* Học cách **cài đặt và cấu hình Splunk** trên máy chủ.
* Hiểu cách **thu thập và phân tích log** hệ thống.
* Sử dụng Splunk để **giám sát và đưa ra báo cáo, cảnh báo** theo thời gian thực.

#### **Yêu cầu đối với Sinh viên**

* Có kiến thức cơ bản về **hệ điều hành Linux**, **mô hình mạng khách/chủ**.
* Hiểu về các loại **log trong hệ điều hành và web**, ví dụ: hệ thống log, log bảo mật, log ứng dụng.

#### **Nội dung Bài Thực Hành**

##### **1. Khởi động Bài Lab:**

1. **Mở terminal** trên hệ thống của bạn.

2. **Khởi động bài lab** bằng câu lệnh sau:

   ```bash
   labtainer -r ptit-splunk-n13
   ```

   **Lưu ý**: Sinh viên cần **sử dụng email stu.ptit.edu.vn** của mình để nhập thông tin người thực hiện bài lab khi có yêu cầu. Thông tin này sẽ được sử dụng để chấm điểm.

3. Sau khi khởi động thành công, **3 terminal ảo sẽ xuất hiện**, mỗi terminal sẽ đại diện cho một máy trong mạng LAN:

   * **Máy Splunk** 
   * **Máy client** 
   * **Máy server**

##### **2. Truy cập Giao Diện Web Splunk:**

1. Trên **terminal của client**, sử dụng lệnh sau để truy cập giao diện web của Splunk:

   ```bash
   firefox http://172.10.0.10:8000
   ```

2. Trên **terminal của server** (container `logserver`), thực hiện các thao tác để **Splunk thu thập log** từ server.

##### **3. Kết Thúc Bài Lab:**

1. Khi kết thúc bài lab, trên **terminal đầu tiên**, sử dụng câu lệnh sau để **dừng bài lab**:

   ```bash
   stoplab ptit-splunk-n13
   ```

2. Sau khi dừng bài lab, một **tệp zip lưu kết quả** sẽ được tạo tại một vị trí được hiển thị dưới câu lệnh `stoplab`.

##### **4. Khởi Động Lại Bài Lab:**

* Trong quá trình làm bài, nếu cần **khởi động lại bài lab**, bạn có thể sử dụng lệnh sau:

  ```bash
  startlab -r ptit-splunk-n13
  ```

---

---

### **Nhiệm Vụ Sinh Viên Cần Thực Hiện**

#### **Nhiệm Vụ 1: Cài đặt và Cấu hình Máy Chủ Log và Splunk**

* Cài đặt và cấu hình Splunk trên máy `splunk` và cấu hình Splunk Forwarder trên máy `logserver` để hai bên giao tiếp và thu thập log.

#### **Nhiệm Vụ 2: Tìm Địa Chỉ IP và Cổng Mở**

* Sử dụng **nmap** để tìm địa chỉ IP và cổng mở trên các máy Splunk và `logserver`.
* Kiểm tra kết nối tới máy chủ web Splunk bằng **ping**.

#### **Nhiệm Vụ 3: Tạo Index và Kiểm Tra Log**

* Tạo một **index** trong Splunk để nhận log từ `logserver`.
* Kiểm tra xem log đã được chuyển tới Splunk chưa bằng truy vấn:

  ```bash
  index="logserver"
  ```

#### **Nhiệm Vụ 4: Tạo Truy Vấn và Bắt Sự Kiện Log**

* Tạo truy vấn trong Splunk để tìm kiếm các sự kiện log trên `logserver`.

#### **Nhiệm Vụ 5: Tạo Báo Cáo (Reports)**

* Tạo **báo cáo** từ các dữ liệu log thu được.

#### **Nhiệm Vụ 6: Tạo Dashboard**

* Tạo **dashboard** từ dữ liệu log để trực quan hóa các thông tin.

#### **Nhiệm Vụ 7: Tạo Cảnh Báo (Alert)**

* Tạo **cảnh báo** khi có sự kiện đăng nhập không hợp lệ hoặc vượt quá số lần cho phép.

---

### **Kết Quả và Chấm Điểm**

Sau khi hoàn thành các nhiệm vụ trên, sinh viên sẽ gửi kết quả dưới dạng tệp zip. Giảng viên sẽ sử dụng các **câu lệnh chấm điểm** để kiểm tra xem sinh viên đã hoàn thành các nhiệm vụ như yêu cầu trong bài thực hành.

---

* **Tải Imodule**: Link tải `imodule.tar`: [https://github.com/khuyennguyen128/AnNinhMang/releases/download/new/imodule.tar](https://github.com/khuyennguyen128/AnNinhMang/releases/download/new/imodule.tar)

