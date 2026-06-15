# NSQ Distributed Messaging System

## Giới thiệu

Đây là dự án nghiên cứu và thực nghiệm hệ thống truyền thông điệp phân tán NSQ (NSQ Distributed Messaging Platform) được phát triển bằng Golang.

Mục tiêu của dự án là tìm hiểu cơ chế hoạt động của hệ thống hàng đợi thông điệp phân tán, khả năng cân bằng tải, khả năng chịu lỗi và phát triển thêm các tính năng giám sát phục vụ quản trị hệ thống.

---

## Dự án gốc

* NSQ: https://github.com/nsqio/nsq
* Ngôn ngữ: Golang
* Kiến trúc: Distributed Messaging System

---

## Mục tiêu

* Tìm hiểu kiến trúc NSQ
* Cài đặt và vận hành hệ thống NSQ
* Thực nghiệm mô hình Producer - Consumer
* Đánh giá khả năng cân bằng tải (Load Balancing)
* Đánh giá khả năng chịu lỗi (Fault Tolerance)
* Phát triển các tính năng mới liên quan đến hệ thống phân tán

---

## Kiến trúc hệ thống

Hệ thống bao gồm:

### NSQLookupd

* Quản lý thông tin các node NSQ
* Hỗ trợ khám phá dịch vụ (Service Discovery)

### NSQD

* Tiếp nhận thông điệp từ Producer
* Lưu trữ và phân phối thông điệp đến Consumer

### NSQAdmin

* Giao diện quản trị hệ thống
* Theo dõi Topic, Channel và Consumer

### Producer

* Gửi thông điệp vào Topic

### Consumer

* Nhận và xử lý thông điệp từ Topic

---

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

---

## Khởi động hệ thống

### Terminal 1

```bash
go run ./apps/nsqlookupd
```

### Terminal 2

```bash
go run ./apps/nsqd --lookupd-tcp-address=127.0.0.1:4160
```

### Terminal 3

```bash
go run ./apps/nsqadmin --lookupd-http-address=127.0.0.1:4161
```

---

## Thực nghiệm

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

Điều này chứng minh NSQ hỗ trợ cân bằng tải giữa các Consumer.

---

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

* Consumer B vẫn tiếp tục nhận và xử lý toàn bộ thông điệp.
* Hệ thống vẫn hoạt động bình thường khi một Consumer ngừng hoạt động.

Điều này chứng minh khả năng chịu lỗi của hệ thống.

---

# Tính năng mới 1

## Priority-Based Topic Classification and Monitoring

### Mô tả

Bổ sung khả năng phân loại Topic theo mức độ ưu tiên.

### Các mức ưu tiên

| Topic       | Priority |
| ----------- | -------- |
| high-alert  | HIGH     |
| normal-task | NORMAL   |
| low-log     | LOW      |

### Lợi ích

* Hỗ trợ quản trị viên nhận biết Topic quan trọng.
* Tăng khả năng giám sát hệ thống.
* Là cơ sở để phát triển cơ chế xử lý ưu tiên trong tương lai.

---

# Tính năng mới 2

## Consumer Uptime Monitoring

### Mô tả

Bổ sung khả năng theo dõi thời gian hoạt động của Consumer trên giao diện NSQ Admin.

### Chức năng

Hiển thị thời gian kết nối của Consumer:

```text
28m57s
30m21s
1h12m
```

### Lợi ích

* Theo dõi độ ổn định của Consumer.
* Hỗ trợ giám sát hệ thống phân tán.
* Hỗ trợ phát hiện Consumer khởi động lại bất thường.

---

## Kiến thức hệ phân tán được áp dụng

* Distributed Messaging
* Publish / Subscribe
* Service Discovery
* Load Balancing
* Fault Tolerance
* Monitoring
* Distributed Consumer Management

---

## Kết quả đạt được

### Hoàn thành

* Cài đặt thành công NSQ
* Thực nghiệm Producer - Consumer
* Kiểm thử Load Balancing
* Kiểm thử Fault Tolerance
* Phát triển 2 tính năng mới
* Quản lý mã nguồn bằng GitHub

### Công nghệ sử dụng

* Golang
* NSQ
* GitHub
* GitHub Codespaces

