# KẾT QUẢ THỰC NGHIỆM HỆ THỐNG NSQ

## 1. Mục tiêu thực nghiệm

Thực nghiệm được tiến hành nhằm đánh giá khả năng xử lý thông điệp trong môi trường phân tán của nền tảng NSQ, đồng thời kiểm tra các đặc tính quan trọng của hệ thống bao gồm khả năng cân bằng tải (Load Balancing) và khả năng chịu lỗi (Fault Tolerance).

Bên cạnh đó, nhóm thực hiện phát triển và đánh giá hai tính năng mở rộng trên giao diện NSQAdmin nhằm nâng cao khả năng giám sát và quản trị hệ thống phân tán.

---

## 2. Môi trường thực nghiệm

| Thành phần                 | Cấu hình                   |
| -------------------------- | -------------------------- |
| Nền tảng thực thi          | GitHub Codespaces          |
| Hệ điều hành               | Ubuntu Linux               |
| Ngôn ngữ lập trình         | Golang                     |
| Hệ thống truyền thông điệp | NSQ                        |
| Công cụ quản trị           | NSQAdmin                   |
| Thành phần sử dụng         | nsqlookupd, nsqd, nsqadmin |
| Consumer                   | Consumer A, Consumer B     |

---

## 3. Thiết lập thực nghiệm

Hệ thống được cấu hình với:

* Topic: **test-topic**
* Channel: **demo-channel**
* Số lượng Consumer: **02**

Hai Consumer cùng đăng ký nhận thông điệp từ một Channel nhằm mô phỏng môi trường xử lý phân tán với nhiều tiến trình tiêu thụ dữ liệu đồng thời.

---

## 4. Kết quả kiểm tra cân bằng tải (Load Balancing)

Tiến hành gửi 100 thông điệp vào Topic **test-topic**.

### Kết quả

* Consumer A nhận và xử lý một phần thông điệp.
* Consumer B nhận và xử lý một phần thông điệp.
* Không xảy ra hiện tượng một Consumer nhận toàn bộ tải xử lý.

NSQ tự động phân phối các thông điệp giữa các Consumer trong cùng một Channel, giúp cân bằng khối lượng công việc và tận dụng hiệu quả tài nguyên hệ thống.

### Đánh giá

Kết quả thực nghiệm cho thấy NSQ hỗ trợ cơ chế cân bằng tải hiệu quả giữa nhiều Consumer, phù hợp với các hệ thống phân tán yêu cầu khả năng mở rộng theo chiều ngang.

---

## 5. Kết quả kiểm tra khả năng chịu lỗi (Fault Tolerance)

Tiến hành dừng hoạt động Consumer A và tiếp tục gửi thêm 20 thông điệp mới vào hệ thống.

### Kết quả

* Consumer B tiếp tục nhận và xử lý toàn bộ các thông điệp mới.
* Hệ thống không bị gián đoạn.
* Không ghi nhận hiện tượng mất thông điệp trong phạm vi kịch bản thực nghiệm.

### Đánh giá

Kết quả cho thấy NSQ vẫn duy trì hoạt động ổn định khi một Consumer gặp sự cố hoặc ngừng hoạt động. Điều này thể hiện khả năng chịu lỗi của hệ thống trong môi trường xử lý phân tán.

---

## 6. Kết quả thực nghiệm tính năng mới 1 – Priority-Based Topic Classification and Monitoring

### Mô tả

Nhóm bổ sung chức năng phân loại Topic theo mức độ ưu tiên trên giao diện NSQAdmin.

Hệ thống tự động xác định mức ưu tiên của Topic dựa trên tên Topic và hiển thị tại cột Priority.

### Kết quả

| Topic       | Priority |
| ----------- | -------- |
| high-alert  | HIGH     |
| normal-task | NORMAL   |
| low-log     | LOW      |
| test-topic  | NORMAL   |

### Đánh giá

Tính năng giúp quản trị viên nhanh chóng nhận biết các Topic quan trọng trong hệ thống có nhiều luồng dữ liệu khác nhau.

Việc phân loại Topic hỗ trợ nâng cao khả năng giám sát và quản lý hệ thống truyền thông điệp phân tán.

### Kiến thức hệ phân tán áp dụng

* Distributed Monitoring
* Message Classification
* Distributed System Management

---

## 7. Kết quả thực nghiệm tính năng mới 2 – Consumer Uptime Monitoring Enhancement

### Mô tả

Nhóm bổ sung khả năng theo dõi thời gian hoạt động của Consumer trên giao diện NSQAdmin.

Thông tin Uptime được cập nhật liên tục kể từ thời điểm Consumer kết nối tới hệ thống.

### Kết quả

| Consumer   | Uptime |
| ---------- | ------ |
| Consumer A | 2m33s  |
| Consumer B | 2m41s  |

### Kiểm thử

| Trường hợp                         | Kết quả                            |
| ---------------------------------- | ---------------------------------- |
| Consumer hoạt động bình thường     | Uptime tăng liên tục               |
| Consumer khởi động lại             | Uptime được đặt lại từ 0           |
| Nhiều Consumer hoạt động đồng thời | Mỗi Consumer hiển thị Uptime riêng |

### Đánh giá

Tính năng cung cấp thêm thông tin vận hành của Consumer, hỗ trợ quản trị viên theo dõi trạng thái hoạt động của các tiến trình xử lý thông điệp trong hệ thống phân tán.

### Kiến thức hệ phân tán áp dụng

* Distributed Consumer Management
* Distributed Monitoring
* Service Availability Observation

---

## 8. Tổng hợp kết quả thực nghiệm

| Nội dung kiểm thử                                  | Kết quả    |
| -------------------------------------------------- | ---------- |
| Producer – Consumer Communication                  | Thành công |
| Load Balancing                                     | Thành công |
| Fault Tolerance                                    | Thành công |
| Priority-Based Topic Classification and Monitoring | Thành công |
| Consumer Uptime Monitoring Enhancement             | Thành công |

---

## 9. Kết luận

Thông qua quá trình thực nghiệm, hệ thống NSQ đã chứng minh được khả năng xử lý thông điệp phân tán hiệu quả, hỗ trợ cân bằng tải giữa nhiều Consumer và duy trì hoạt động ổn định khi xảy ra sự cố tại một thành phần của hệ thống.

Bên cạnh việc đánh giá các đặc tính phân tán cốt lõi của NSQ, nhóm đã phát triển và tích hợp thành công hai tính năng mới gồm:

* Priority-Based Topic Classification and Monitoring.
* Consumer Uptime Monitoring Enhancement.

Các tính năng này góp phần nâng cao khả năng giám sát, quản trị và theo dõi hoạt động của hệ thống phân tán trên giao diện NSQAdmin.

Kết quả đạt được phù hợp với mục tiêu nghiên cứu và cho thấy NSQ là một nền tảng truyền thông điệp phân tán có khả năng mở rộng, độ tin cậy cao và thuận lợi cho việc phát triển các chức năng mở rộng phục vụ vận hành thực tế.
