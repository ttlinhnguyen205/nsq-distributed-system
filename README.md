# NSQ Distributed Messaging System

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

# Tính năng mới 1

## Priority Topic Monitoring

### Mô tả

Bổ sung khả năng phân loại Topic theo mức độ ưu tiên trên giao diện NSQAdmin.

### Các mức ưu tiên

| Topic       | Priority |
| ----------- | -------- |
| high-alert  | HIGH     |
| normal-task | NORMAL   |
| low-log     | LOW      |

### Lợi ích

* Hỗ trợ quản trị viên nhận biết Topic quan trọng
* Tăng khả năng giám sát hệ thống
* Hỗ trợ ưu tiên theo dõi các luồng dữ liệu quan trọng
* Là cơ sở để phát triển cơ chế xử lý ưu tiên trong tương lai


# Tính năng mới 2

## Consumer Uptime Monitoring

### Mô tả

Bổ sung khả năng theo dõi thời gian hoạt động của Consumer trên giao diện NSQAdmin.

### Chức năng

Hiển thị thời gian kết nối của Consumer:

```text
28m57s
30m21s
1h12m
```

### Lợi ích

* Theo dõi độ ổn định của Consumer
* Hỗ trợ giám sát hệ thống phân tán
* Hỗ trợ phát hiện Consumer khởi động lại bất thường
* Hỗ trợ quản trị viên đánh giá trạng thái hoạt động của Consumer

## Kết quả thực nghiệm

| Nội dung                          | Kết quả    |
| --------------------------------- | ---------- |
| NSQ Installation                  | Thành công |
| Producer – Consumer Communication | Thành công |
| Load Balancing                    | Thành công |
| Fault Tolerance                   | Thành công |
| Priority Topic Monitoring         | Thành công |
| Consumer Uptime Monitoring        | Thành công |

---

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

| Họ tên               | Công việc                                                                                              |
| -------------------- | ------------------------------------------------------------------------------------------------------ |
| Nguyễn Thị Thuỳ Linh | Cài đặt và cấu hình hệ thống NSQ, phát triển tính năng Priority Topic Monitoring, xây dựng báo cáo     |
| Nguyễn Anh Quân      | Thực nghiệm hệ thống, phát triển tính năng Consumer Uptime Monitoring, kiểm thử và hoàn thiện tài liệu |


## Kết luận

Dự án đã cài đặt và thực nghiệm thành công hệ thống truyền thông điệp phân tán NSQ. Nhóm đã đánh giá được các đặc tính quan trọng của hệ thống gồm khả năng cân bằng tải và khả năng chịu lỗi. Đồng thời nhóm đã phát triển thành công hai tính năng mới là Priority Topic Monitoring và Consumer Uptime Monitoring nhằm hỗ trợ giám sát và quản trị hệ thống phân tán hiệu quả hơn.
