# KẾT QUẢ THỰC NGHIỆM HỆ THỐNG NSQ

## 1. Mục tiêu thực nghiệm

Thực nghiệm nhằm kiểm tra khả năng xử lý thông điệp phân tán, cân bằng tải và khả năng chịu lỗi của nền tảng NSQ.

## 2. Môi trường thực nghiệm

* Nền tảng: GitHub Codespaces
* Ngôn ngữ lập trình: Golang
* Hệ thống truyền thông điệp: NSQ
* Thành phần sử dụng:

  * nsqlookupd
  * nsqd
  * nsqadmin
  * Consumer A
  * Consumer B

## 3. Thiết lập thực nghiệm

* Topic: test-topic
* Channel: demo-channel

Hệ thống được cấu hình với hai Consumer cùng đăng ký nhận thông điệp từ một Channel.

## 4. Kết quả kiểm tra cân bằng tải

Tiến hành gửi 100 thông điệp vào Topic test-topic.

Kết quả:

* Consumer A nhận một phần thông điệp.
* Consumer B nhận một phần thông điệp.

Các thông điệp được NSQ tự động phân phối giữa hai Consumer, chứng minh cơ chế cân bằng tải và xử lý song song của hệ thống.

## 5. Kết quả kiểm tra khả năng chịu lỗi

Tiến hành dừng Consumer A và tiếp tục gửi thêm 20 thông điệp mới.

Kết quả:

* Consumer B tiếp tục nhận và xử lý toàn bộ các thông điệp từ 101 đến 120.
* Hệ thống vẫn hoạt động bình thường mặc dù một Consumer đã ngừng hoạt động.

Điều này chứng minh NSQ có khả năng chịu lỗi ở mức Consumer và đảm bảo hệ thống tiếp tục xử lý khi xảy ra sự cố.

## 6. Kết luận

Kết quả thực nghiệm cho thấy NSQ hỗ trợ hiệu quả việc xử lý thông điệp phân tán, cân bằng tải giữa nhiều Consumer và duy trì hoạt động ổn định khi một thành phần trong hệ thống gặp sự cố.
