---
title: "Truyền thông - Bài tập 3"
date: "2025-05-06"
updated: "2025-05-06"
categories:
  - sveltekit
  - markdown
  - svelte
coverImage: "/images/anh1.png"
coverWidth: 16
coverHeight: 9
excerpt: "This post demonstrates how to include a Svelte component in a Markdown post."
---
# Bài tập 1: Tìm hiểu cơ chế, chức năng và cài đặt RabbitMQ

### 1. Giới thiệu về RabbitMQ
- RabbitMQ là một hệ thống message broker được sử dụng rộng rãi trong các hệ thống phân tán để truyền thông điệp giữa các thành phần không đồng bộ.
- RabbitMQ hỗ trợ giao thức AMQP (Advanced Message Queuing Protocol) và cho phép giao tiếp giữa các dịch vụ một cách tin cậy và linh hoạt.

### 2. Cơ chế hoạt động của RabbitMQ
RabbitMQ là một message broker đứng giữa Producer và Consumer:
- Producer: Gửi thông điệp
- Consumer: Nhận thông điệp
- Exchange: Quyết định cách thông điệp được định tuyến đến Queue
- Queue: Hàng đợi nơi lưu trữ thông điệp

Quy trình:
- Producer gửi message đến Exchange
- Exchange định tuyến message đến một (hoặc nhiều) Queue
- Consumer lấy message từ Queue ra xử lý

### 3. Các chức năng chính của RabbitMQ
- Giao tiếp phi đồng bộ: Producer và Consumer không cần chờ nhau
- Cân bằng tải: Nhiều Consumer có thể chia nhau message trong một Queue
- Đảm bảo tin cậy: Thông qua ack, retry, và durable queue
- Hỗ trợ nhiều loại Exchange:
  - Direct: gửi theo routing key chỉnh xác
  - Fanout: gửi đến tất cả Queue
  - Topic: routing key theo mẫu
  - Headers: theo các header metadata

### 4. Cài đặt RabbitMQ

#### Cách 1: Cài RabbitMQ qua Docker
``` Tải về và chạy container
sudo docker run -d --hostname my-rabbit --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management
```
- Port 5672: cho ứng dụng kết nối
- Port 15672: truy cập giao diện web (http://localhost:15672)
- Mặc định user/password: guest/guest

#### Cách 2: Cài trực tiếp trên Ubuntu
```
sudo apt update
sudo apt install rabbitmq-server
sudo systemctl enable rabbitmq-server
sudo systemctl start rabbitmq-server
```
Sau khi cài xong:
```
sudo rabbitmq-plugins enable rabbitmq_management
```
Truy cập: http://localhost:15672

### 5. Kết luận

RabbitMQ là một message broker được dùng phổ biến nhờ vào cơ chế truyền thông hiệu quả, dễ sử dụng, và tính linh hoạt. Nó thích hợp cho các hệ thống vi dịch vụ, event-driven hay các hệ thống yêu cầu giao tiếp không đồng bộ.

# Bài tập 2: 
Để thực hiện bài tập sử dụng RabbitMQ trong Python, chúng ta sẽ tạo một hệ thống với hai phần chính:
1. **Producer**: Nhận nội dung từ bàn phím và gửi tin nhắn vào hàng đợi.
2. **Consumer**: Nhận tin nhắn từ hàng đợi và hiển thị chúng.
Chúng ta sẽ sử dụng thư viện **`pika`** trong Python để kết nối với RabbitMQ.
### 1. **Cài đặt RabbitMQ và Thư viện Pika**
Trước hết, bạn cần cài đặt RabbitMQ (nếu chưa có) và thư viện `pika` trong Python.
- Cài đặt RabbitMQ: Bạn có thể tải RabbitMQ từ trang chính thức của [RabbitMQ](https://www.rabbitmq.com/download.html) hoặc cài đặt thông qua `brew` trên macOS.
- Cài đặt thư viện `pika`:
```bash
pip install pika
```

### 2. **Tạo Producer**
**Producer** sẽ gửi tin nhắn vào hàng đợi. Cụ thể, chương trình này sẽ nhận nội dung từ bàn phím và gửi nó vào hàng đợi của RabbitMQ.
```python
import pika

# Kết nối đến RabbitMQ server
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# Đảm bảo rằng queue 'hello' tồn tại
channel.queue_declare(queue='hello')

# Nhập tin nhắn từ bàn phím
message = input("Enter a message to send to the queue: ")

# Gửi tin nhắn vào hàng đợi 'hello'
channel.basic_publish(exchange='',
                      routing_key='hello',
                      body=message)

print(f" [x] Sent {message}")

# Đóng kết nối
connection.close()
```
**Giải thích**:
- `pika.BlockingConnection(pika.ConnectionParameters('localhost'))`: Kết nối đến RabbitMQ trên localhost.
- `channel.queue_declare(queue='hello')`: Đảm bảo hàng đợi `hello` tồn tại. Nếu không, nó sẽ được tạo ra.
- `channel.basic_publish`: Gửi tin nhắn vào hàng đợi `hello` với nội dung từ bàn phím.

### 3. **Tạo Consumer**
**Consumer** sẽ nhận tin nhắn từ hàng đợi và hiển thị chúng lên màn hình.
```python
import pika

# Kết nối đến RabbitMQ server
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# Đảm bảo rằng queue 'hello' tồn tại
channel.queue_declare(queue='hello')

# Hàm xử lý khi nhận tin nhắn
def callback(ch, method, properties, body):
    print(f" [x] Received {body.decode()}")

# Đăng ký Consumer để nhận tin nhắn từ hàng đợi 'hello'
channel.basic_consume(queue='hello', on_message_callback=callback, auto_ack=True)

print(' [*] Waiting for messages. To exit press CTRL+C')
# Bắt đầu nhận tin nhắn
channel.start_consuming()
```
**Giải thích**:
- `channel.basic_consume`: Đăng ký một Consumer để nhận tin nhắn từ hàng đợi `hello`. Khi nhận được tin nhắn, hàm `callback` sẽ được gọi.
- `callback`: Hàm này sẽ in ra tin nhắn khi nhận được.
- `channel.start_consuming()`: Bắt đầu nhận tin nhắn từ hàng đợi.
### 4. **Chạy Hệ Thống**
- **Bước 1**: Mở terminal và chạy **Consumer** trước để nó bắt đầu lắng nghe hàng đợi.\
  ```bash
  python consumer.py
  ```
- **Bước 2**: Mở một terminal khác và chạy **Producer**. Sau đó nhập nội dung tin nhắn từ bàn phím và gửi nó vào hàng đợi.
  ```bash
  python producer.py
  ```
**Kết quả**: Sau khi bạn nhập một tin nhắn trong Producer, nó sẽ được gửi vào hàng đợi `hello`, và Consumer sẽ nhận và hiển thị tin nhắn đó.

Ví dụ:
```bash
[*] Waiting for messages. To exit press CTRL+C
Enter a message to send to the queue: Hello RabbitMQ!
[x] Sent Hello RabbitMQ!
```

Và Consumer sẽ in ra:
```bash
[x] Received Hello RabbitMQ!
```

### 5. **Kết luận**
Hệ thống này sử dụng RabbitMQ để gửi và nhận tin nhắn giữa các thành phần. **Producer** gửi tin nhắn vào hàng đợi, và **Consumer** nhận và xử lý tin nhắn đó. Đây là một ví dụ đơn giản về cách sử dụng RabbitMQ trong Python với thư viện `pika` để xây dựng hệ thống giao tiếp giữa các dịch vụ hoặc phần mềm phân tán.


# Bài tập 3:Báo cáo tìm hiểu và demo một thư viện RPC sử dụng định dạng JSON
#### 1. **Giới thiệu về RPC và các giao thức RPC phổ biến**
RPC (Remote Procedure Call) là một giao thức cho phép một chương trình thực thi một hàm hoặc phương thức trên máy chủ từ xa, giống như việc gọi một hàm hoặc phương thức trong cùng một chương trình. RPC giúp các hệ thống phân tán giao tiếp và tương tác với nhau.

Các giao thức RPC phổ biến bao gồm:

- **XML-RPC**: Sử dụng XML để mã hóa các yêu cầu và phản hồi.
- **JSON-RPC**: Tương tự như XML-RPC, nhưng sử dụng JSON làm định dạng mã hóa, giúp việc trao đổi dữ liệu dễ dàng và nhẹ nhàng hơn.
- **gRPC**: Sử dụng Protocol Buffers (protobuf) để mã hóa và HTTP/2 cho giao tiếp hiệu quả, hỗ trợ nhiều ngôn ngữ lập trình.
- **Thư viện khác**: Một số thư viện khác có thể sử dụng JSON như WebSockets kết hợp với JSON-RPC.

Trong báo cáo này, chúng ta sẽ tìm hiểu và thực hiện demo sử dụng thư viện **JSON-RPC**, một giao thức RPC đơn giản sử dụng định dạng JSON.

#### 2. **JSON-RPC là gì?**
JSON-RPC là một giao thức RPC sử dụng JSON làm định dạng truyền tải. JSON-RPC không yêu cầu bất kỳ chi tiết cụ thể nào về cấu trúc phương thức gọi, và đơn giản trong việc triển khai, dễ sử dụng cho các ứng dụng web, microservices và các hệ thống phân tán.

**Đặc điểm của JSON-RPC**:
- **Đơn giản và nhẹ**: JSON-RPC sử dụng JSON, dễ đọc và dễ hiểu.
- **Khả năng mở rộng**: Hỗ trợ nhiều phương thức từ xa trong một giao tiếp.
- **Giao thức không đồng bộ**: Thích hợp cho các hệ thống cần xử lý đồng thời.
- **Không yêu cầu trạng thái**: Giao thức có thể chạy trên các kết nối không trạng thái.

#### 3. **Cài đặt thư viện JSON-RPC**
Chúng ta sẽ sử dụng thư viện `json-rpc` trong Python để triển khai một ví dụ đơn giản. Để bắt đầu, bạn cần cài đặt thư viện này.

**Cài đặt thư viện**:
```bash
pip install json-rpc
```

#### 4. **Code Demo**
**4.1. Code Server (Python)**
Chúng ta sử dụng Flask để tạo một server đơn giản tiếp nhận các yêu cầu JSON-RPC.

```python
from jsonrpc import JSONRPCResponseManager, dispatcher
from flask import Flask, request

app = Flask(__name__)

# Định nghĩa phương thức RPC
@dispatcher.add_method
def add(x, y):
    return x + y

@app.route("/api", methods=["POST"])
def api():
    response = JSONRPCResponseManager.handle(request.get_data().decode(), dispatcher)
    return response.json

if __name__ == "__main__":
    app.run(debug=True)
```

Trong đoạn mã trên:
- Server sử dụng Flask để nhận yêu cầu HTTP POST tại `/api`.
- Phương thức `add(x, y)` thực hiện phép cộng hai số và trả về kết quả.

**4.2. Code Client (Python)**
Client sẽ gửi yêu cầu RPC tới server và nhận phản hồi.

```python
import requests
import json

url = "http://localhost:5000/api"
headers = {'Content-Type': 'application/json'}

# Yêu cầu RPC gửi tới server
data = {
    "jsonrpc": "2.0",
    "method": "add",
    "params": [10, 20],
    "id": 1
}

response = requests.post(url, data=json.dumps(data), headers=headers)

# In kết quả trả về
print(response.json())
```

Trong đoạn mã trên:
- Client gửi yêu cầu POST với dữ liệu JSON đến server.
- Yêu cầu này yêu cầu thực hiện phương thức `add` với các tham số là `10` và `20`.
- Server phản hồi với kết quả là `30`.

**4.3. Chạy ứng dụng**
- Chạy server bằng cách chạy mã nguồn server trên máy chủ.
- Sau đó, chạy client và bạn sẽ nhận được kết quả trả về từ server.

Kết quả khi chạy client sẽ là:
```json
{
    "jsonrpc": "2.0",
    "result": 30,
    "id": 1
}
```

#### 5. **Kết luận**
Trong báo cáo này, chúng ta đã tìm hiểu về giao thức **JSON-RPC**, một giao thức RPC sử dụng định dạng JSON để truyền tải các yêu cầu và phản hồi giữa client và server.
Ưu điểm của JSON-RPC là sự đơn giản và tính nhẹ nhàng, dễ dàng triển khai cho các ứng dụng web và hệ thống phân tán. Thư viện `json-rpc` trong Python cho phép triển khai các phương thức gọi từ xa một cách dễ dàng và nhanh chóng.
So với các giao thức RPC khác như **XML-RPC**, JSON-RPC có lợi thế là sử dụng JSON – một định dạng dễ đọc và phổ biến, giúp tiết kiệm tài nguyên và tối ưu hiệu suất trong giao tiếp giữa các dịch vụ.
Hy vọng thông qua ví dụ trên, bạn có thể dễ dàng triển khai và sử dụng JSON-RPC trong các hệ thống của mình!
