# NSQ Distributed Messaging System
# Demo: 
## Giới thiệu

Đây là dự án nghiên cứu và thực nghiệm hệ thống truyền thông điệp phân tán NSQ (NSQ Distributed Messaging Platform) được phát triển bằng Golang.

Mục tiêu của dự án là tìm hiểu cơ chế hoạt động của hệ thống hàng đợi thông điệp phân tán, khả năng cân bằng tải, khả năng chịu lỗi và phát triển thêm các tính năng giám sát phục vụ quản trị hệ thống.

## Dự án gốc

* NSQ: https://github.com/nsqio/nsq
* Ngôn ngữ: Golang
* Kiến trúc: Distributed Messaging System
* Giấy phép: MIT License

## Mục tiêu

* Tìm hiểu kiến trúc NSQ
* Cài đặt và vận hành hệ thống NSQ
* Thực nghiệm mô hình Producer – Consumer
* Đánh giá khả năng cân bằng tải (Load Balancing)
* Đánh giá khả năng chịu lỗi (Fault Tolerance)
* Phát triển các tính năng mới liên quan đến hệ thống phân tán

## Kiến trúc hệ thống

Hệ thống bao gồm các thành phần chính:

### 1. NSQLookupd

* Quản lý thông tin các node NSQ
* Hỗ trợ khám phá dịch vụ (Service Discovery)
* Kết nối Consumer với các NSQD đang hoạt động

### 2. NSQD

* Tiếp nhận thông điệp từ Producer
* Lưu trữ thông điệp trong bộ nhớ hoặc trên đĩa
* Phân phối thông điệp đến Consumer
* Theo dõi trạng thái xử lý thông điệp

### 3. NSQAdmin

* Giao diện quản trị hệ thống
* Theo dõi Topic, Channel và Consumer
* Giám sát trạng thái hoạt động của hệ thống

### 4. Producer

* Gửi thông điệp vào Topic

### 5. Consumer

* Nhận và xử lý thông điệp từ Topic thông qua Channel

## Cài đặt

### Clone dự án

```bash
git clone https://github.com/ttlinhnguyen205/nsq-distributed-system.git
cd nsq-distributed-system
```

### Build NSQ

```bash
make
```

## Khởi động hệ thống

### Terminal 1 – NSQLookupd

```bash
go run ./apps/nsqlookupd
```

### Terminal 2 – NSQD

```bash
go run ./apps/nsqd --lookupd-tcp-address=127.0.0.1:4160
```

### Terminal 3 – NSQAdmin

```bash
go run ./apps/nsqadmin --lookupd-http-address=127.0.0.1:4161
```

### Truy cập giao diện quản trị

```text
http://localhost:4171
```

## Thực nghiệm hệ thống

### Kiểm thử cân bằng tải (Load Balancing)

Khởi động 2 Consumer:

```bash
go run ./apps/nsq_to_file \
--topic=test-topic \
--channel=demo-channel \
--lookupd-http-address=127.0.0.1:4161 \
--output-dir=/tmp/nsq-consumer-a
```

```bash
go run ./apps/nsq_to_file \
--topic=test-topic \
--channel=demo-channel \
--lookupd-http-address=127.0.0.1:4161 \
--output-dir=/tmp/nsq-consumer-b
```

Gửi 100 thông điệp:

```bash
for i in {1..100}; do
curl -s -d "Distributed Message $i" \
"http://127.0.0.1:4151/pub?topic=test-topic"
done
```

### Kết quả

* Consumer A nhận một phần thông điệp
* Consumer B nhận một phần thông điệp
* Tải được phân phối giữa các Consumer

Kết quả cho thấy NSQ hỗ trợ cơ chế cân bằng tải trong môi trường phân tán.

## Kiểm thử khả năng chịu lỗi (Fault Tolerance)

Dừng Consumer A:

```bash
Ctrl + C
```

Tiếp tục gửi thông điệp:

```bash
for i in {101..120}; do
curl -s -d "Message $i" \
"http://127.0.0.1:4151/pub?topic=test-topic"
done
```

### Kết quả

* Consumer B tiếp tục nhận và xử lý thông điệp
* Hệ thống không bị gián đoạn
* Không xảy ra mất dữ liệu

Kết quả cho thấy NSQ có khả năng chịu lỗi khi một Consumer ngừng hoạt động.

# Tính năng mới 1: Consumer Load Balancing Monitor

### Mô tả

Bổ sung khả năng giám sát mức tải xử lý của Consumer trên giao diện NSQAdmin.

Hệ thống tự động đánh giá mức tải dựa trên số lượng thông điệp mà Consumer đã xử lý và hiển thị trực quan dưới dạng Load Level.

### Các mức tải

| Load Level  | Ý nghĩa                                       |
| ----------- | --------------------------------------------- |
| HIGH LOAD   | Consumer đang xử lý khối lượng thông điệp lớn |
| MEDIUM LOAD | Consumer đang xử lý mức tải trung bình        |
| LOW LOAD    | Consumer đang xử lý ít thông điệp             |

### Lợi ích

* Hỗ trợ giám sát hoạt động cân bằng tải giữa các Consumer
* Giúp phát hiện Consumer đang quá tải
* Hỗ trợ đánh giá hiệu quả phân phối thông điệp của hệ thống
* Cung cấp thông tin trực quan cho quản trị viên

### Kiến thức hệ phân tán áp dụng

* Load Balancing
* Scalability
* Distributed Message Processing

# Tính năng mới 2: Distributed Node Health Monitoring

### Mô tả

Bổ sung khả năng giám sát trạng thái hoạt động của các node NSQD trên giao diện NSQAdmin.

Hệ thống tự động kiểm tra trạng thái kết nối của node và hiển thị thông tin Node Health cho quản trị viên.

### Trạng thái hỗ trợ

| Node Health | Ý nghĩa                                               |
| ----------- | ----------------------------------------------------- |
| UP          | Node hoạt động bình thường                            |
| WARNING     | Node có dấu hiệu bất thường hoặc mất kết nối một phần |

### Lợi ích

* Hỗ trợ giám sát tình trạng hoạt động của các node phân tán
* Phát hiện sớm các sự cố trong hệ thống
* Hỗ trợ đánh giá tính sẵn sàng của hệ thống
* Nâng cao khả năng quản trị và vận hành

### Kiến thức hệ phân tán áp dụng

* Fault Detection
* Fault Tolerance
* Availability
* Distributed Monitoring


## Kết quả thực nghiệm

| Nội dung                           | Kết quả    |
| ---------------------------------- | ---------- |
| NSQ Installation                   | Thành công |
| Producer – Consumer Communication  | Thành công |
| Load Balancing                     | Thành công |
| Fault Tolerance                    | Thành công |
| Consumer Load Balancing Monitor    | Thành công |
| Distributed Node Health Monitoring | Thành công |


## Kiến thức hệ phân tán được áp dụng

* Distributed Messaging
* Publish / Subscribe
* Service Discovery
* Load Balancing
* Fault Tolerance
* Monitoring
* Distributed Consumer Management

## Công nghệ sử dụng

* Golang
* NSQ
* GitHub
* GitHub Codespaces

## Thành viên thực hiện

| Họ tên               | Công việc                                                                                                      |
| -------------------- | -------------------------------------------------------------------------------------------------------------- |
| Nguyễn Thị Thuỳ Linh | Cài đặt và cấu hình hệ thống NSQ, phát triển tính năng Consumer Load Balancing Monitor, xây dựng báo cáo       |
| Nguyễn Anh Quân      | Thực nghiệm hệ thống, phát triển tính năng Distributed Node Health Monitoring, kiểm thử và hoàn thiện tài liệu |


## Future Work

* Dead Letter Queue (DLQ)
* Consumer Failure Detection
* Multi-Node Monitoring

## Kết luận

Dự án đã cài đặt và thực nghiệm thành công hệ thống truyền thông điệp phân tán NSQ. Nhóm đã đánh giá được các đặc tính quan trọng của hệ thống gồm khả năng cân bằng tải và khả năng chịu lỗi. Đồng thời nhóm đã phát triển thành công hai tính năng mới là Priority-Based Topic Classification and Monitoring và Consumer Health Monitoring nhằm hỗ trợ giám sát và quản trị hệ thống phân tán hiệu quả hơn.
