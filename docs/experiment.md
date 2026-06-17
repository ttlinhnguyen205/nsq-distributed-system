# KẾT QUẢ THỰC NGHIỆM HỆ THỐNG NSQ

## 1. Mục tiêu thực nghiệm

Thực nghiệm được tiến hành nhằm đánh giá khả năng xử lý thông điệp trong môi trường phân tán của nền tảng NSQ, đồng thời kiểm tra các đặc tính quan trọng của hệ thống bao gồm khả năng cân bằng tải (Load Balancing) và khả năng chịu lỗi (Fault Tolerance).

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

## 3. Thiết lập thực nghiệm

Hệ thống được cấu hình với:

* Topic: **test-topic**
* Channel: **demo-channel**
* Số lượng Consumer: **02**

Hai Consumer cùng đăng ký nhận thông điệp từ một Channel nhằm mô phỏng môi trường xử lý phân tán với nhiều tiến trình tiêu thụ dữ liệu đồng thời.

## 4. Kết quả kiểm tra cân bằng tải (Load Balancing)

Tiến hành gửi 100 thông điệp vào Topic **test-topic**.

### Kết quả

* Consumer A nhận và xử lý một phần thông điệp.
* Consumer B nhận và xử lý một phần thông điệp.
* Không xảy ra hiện tượng một Consumer nhận toàn bộ tải xử lý.

NSQ tự động phân phối các thông điệp giữa các Consumer trong cùng một Channel, giúp cân bằng khối lượng công việc và tận dụng hiệu quả tài nguyên hệ thống.

### Đánh giá

Kết quả thực nghiệm cho thấy NSQ hỗ trợ cơ chế cân bằng tải hiệu quả giữa nhiều Consumer, phù hợp với các hệ thống phân tán yêu cầu khả năng mở rộng theo chiều ngang.

## 5. Kết quả kiểm tra khả năng chịu lỗi (Fault Tolerance)

Tiến hành dừng hoạt động Consumer A và tiếp tục gửi thêm 20 thông điệp mới vào hệ thống.

### Kết quả

* Consumer B tiếp tục nhận và xử lý toàn bộ các thông điệp mới.
* Hệ thống không bị gián đoạn.
* Không ghi nhận hiện tượng mất thông điệp trong phạm vi kịch bản thực nghiệm.

### Đánh giá

Kết quả cho thấy NSQ vẫn duy trì hoạt động ổn định khi một Consumer gặp sự cố hoặc ngừng hoạt động. Điều này thể hiện khả năng chịu lỗi của hệ thống trong môi trường xử lý phân tán.

## 6. Kết luận

Thông qua quá trình thực nghiệm, hệ thống NSQ đã chứng minh được khả năng xử lý thông điệp phân tán hiệu quả, hỗ trợ cân bằng tải giữa nhiều Consumer và duy trì hoạt động ổn định khi xảy ra sự cố tại một thành phần của hệ thống. Kết quả đạt được phù hợp với mục tiêu nghiên cứu và cho thấy NSQ là một nền tảng truyền thông điệp phân tán có khả năng mở rộng và độ tin cậy cao.
