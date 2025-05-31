---
title: "Deliverable 2 : Website"
date: "2025-05-31"
updated: "2025-05-31"
categories:
  - "sveltekit"
  - "markdown"
  - "svelte"
coverImage: "/images/jerry-zhang-ePpaQC2c1xA-unsplash.jpg"
coverWidth: 16
coverHeight: 9
excerpt: This post demonstrates how to include a Svelte component in a Markdown post.
---

## BÁO CÁO ĐỒ ÁN: Hệ thống quản lý dữ liệu cảm biến (IoT) sử dụng ScyllaDB
### 1. Bản vẽ kiến trúc hệ thống
Sơ đồ kiến trúc hệ thống mô tả luồng dữ liệu từ các thiết bị cảm biến, qua MQTT Broker, xử lý bởi dịch vụ thu thập dữ liệu, lưu trữ tại cụm cơ sở dữ liệu ScyllaDB và truy xuất dữ liệu thông qua REST API lên giao diện người dùng. Hệ thống đảm bảo tính mở rộng, thời gian thực và hiệu suất cao.
Hình minh họa sơ đồ kiến trúc:
![Bảng ví dụ](/images/sdkt.png)

### 2. Mô tả chi tiết các thành phần trong hệ thống
Dưới đây là phần **giải thích chi tiết các thành phần chính trong hệ thống quản lý dữ liệu cảm biến (IoT) sử dụng ScyllaDB**, bao gồm vai trò và cách hoạt động của từng thành phần, cùng với cơ chế **sharding**, **replication** và thuật toán được sử dụng:
#### **2.1. Sensor Devices (Thiết bị cảm biến)**

* **Vai trò:** Thu thập dữ liệu môi trường như nhiệt độ, độ ẩm, ánh sáng,...
* **Hoạt động:** Gửi dữ liệu đo được theo thời gian thực đến hệ thống thông qua giao thức **MQTT**.
* **Giao thức:** MQTT (Message Queuing Telemetry Transport) – nhẹ, tối ưu cho thiết bị IoT.

#### **22.2. MQTT Broker (e.g. Mosquitto)**

* **Vai trò:** Trung gian nhận và phân phối thông điệp từ các thiết bị cảm biến đến các hệ thống xử lý.
* **Hoạt động:**

  * Thiết bị **publish** dữ liệu lên các topic.
  * Dịch vụ xử lý dữ liệu **subscribe** để nhận dữ liệu.
* **Lý do chọn:** Mosquitto là broker nhẹ, hiệu quả cao cho môi trường tài nguyên thấp.


#### **2.3. Data Ingestion Service (Python/Node.js)**

* **Vai trò:** Nhận dữ liệu từ MQTT Broker, xử lý và lưu trữ vào ScyllaDB.
* **Chức năng chính:**

  * Parse dữ liệu JSON từ cảm biến.
  * Xử lý logic đơn giản như làm sạch, chuẩn hóa dữ liệu.
  * Gửi truy vấn ghi (INSERT) vào ScyllaDB.
* **Giao tiếp:** Thông qua **thư viện MQTT client** và **ScyllaDB driver** (cassandra-driver cho Python).

#### **2.4. ScyllaDB Cluster (Node1, Node2, Node3)**

* **Vai trò:** Lưu trữ dữ liệu cảm biến lớn với hiệu suất cao, khả năng mở rộng tốt.
* **Cơ chế hoạt động:**

  * **Sharding:** Dữ liệu được **tự động chia nhỏ (shard)** trên nhiều node bằng **token-based sharding**.
    → Mỗi node chỉ xử lý một phần dữ liệu → tối ưu truy vấn.
  * **Replication:** Mỗi bản ghi được **sao chép sang nhiều node** theo **replication factor (RF)**.
    → Ví dụ: RF = 3 → mỗi dữ liệu có 3 bản trên 3 node khác nhau.
* **Thuật toán đồng thuận:** **Gossip Protocol** + **Tunable Consistency**.
  → Cho phép chọn mức độ nhất quán theo từng truy vấn (e.g. `QUORUM`, `ALL`, `ONE`).

#### **2.5. REST API Service (Python Flask, Node.js Express, v.v)**

* **Vai trò:** Là trung gian giữa frontend và database.
* **Chức năng:**

  * Nhận yêu cầu từ frontend (GET/POST).
  * Truy vấn dữ liệu từ ScyllaDB và trả về kết quả.
  * Xử lý xác thực (nếu có).
* **Lý do chọn:** REST API dễ tích hợp và nhẹ cho các ứng dụng IoT.
 
#### **2.6. Frontend Dashboard (HTML/CSS/JS, React, hoặc Vue)**

* **Vai trò:** Giao diện hiển thị dữ liệu cảm biến cho người dùng cuối.
* **Chức năng:**

  * Gửi yêu cầu đến REST API.
  * Hiển thị dữ liệu dạng bảng, biểu đồ, theo thời gian thực.
  * Có thể thêm tính năng cảnh báo nếu dữ liệu vượt ngưỡng.
 
####  **2.7. Giải thích Sharding & Replication trong ScyllaDB**

##### 🔹 **Sharding (Phân mảnh dữ liệu):**

* **Cơ chế:** Dữ liệu được phân bổ theo giá trị **partition key** qua **vòng băm (token ring)**.
* **Thuật toán:** **Consistent Hashing**.
* **Lợi ích:**

  * Cân bằng tải giữa các node.
  * Truy vấn chỉ gửi đến node chứa shard liên quan.

##### 🔹 **Replication (Sao chép dữ liệu):**

* **Cơ chế:** Dữ liệu được sao chép sang nhiều node khác nhau.
* **Cài đặt:** `Replication Factor` (ví dụ: 3).
* **Giao thức:** **Gossip Protocol** giúp các node đồng bộ trạng thái, phát hiện lỗi node.
* **Lợi ích:**

  * Tăng tính sẵn sàng và độ tin cậy.
  * Node hỏng vẫn có bản sao dữ liệu ở node khác.

#####  Tổng kết
![Bảng ví dụ](/images/tongket.png)


### 3. Công nghệ và thư viện sử dụng
![Bảng ví dụ](/images/lydo.png)

### 4. Mô hình dữ liệu (Database Model)
#### 4.1. Giới thiệu chung
* Hệ thống lưu trữ dữ liệu cảm biến thời gian thực với lượng lớn dữ liệu liên tục từ nhiều thiết bị IoT. Do đó, mô hình dữ liệu cần tối ưu cho việc ghi nhanh, mở rộng theo chiều ngang, và truy vấn hiệu quả dựa trên các khóa phân vùng (partition keys) phù hợp.

* ScyllaDB là hệ quản trị cơ sở dữ liệu NoSQL dạng wide-column store, tương tự Cassandra, hỗ trợ mô hình dữ liệu phân tán, chịu tải cao, có khả năng sharding và replication tự động.

#### 4.2. Các bảng chính
![Bảng ví dụ](/images/bang12.png)

#### 4.3. Chi tiết bảng sensor_data
```
CREATE TABLE sensor_data (
    sensor_id text,
    timestamp timestamp,
    temperature float,
    humidity float,
    light int,
    PRIMARY KEY (sensor_id, timestamp)
) WITH CLUSTERING ORDER BY (timestamp DESC);
```
- Partition key: sensor_id — đảm bảo dữ liệu từ cùng một cảm biến được lưu trên cùng một node, tối ưu truy vấn theo cảm biến.
- Clustering key: timestamp — sắp xếp các bản ghi theo thời gian giảm dần, giúp truy vấn dữ liệu mới nhất dễ dàng.
- Hỗ trợ truy vấn: Lấy dữ liệu cảm biến theo ID và khoảng thời gian (ví dụ: 1 giờ gần nhất).

#### 4.4. BBảng sensor_metadata
```
CREATE TABLE sensor_metadata (
    sensor_id text PRIMARY KEY,
    location text,
    sensor_type text,
    installation_date date
);
```
- Lưu thông tin cấu hình cảm biến.
- Giúp quản trị viên hoặc hệ thống biết được cảm biến nào ở đâu, loại nào để phục vụ truy vấn và báo cáo.

#### 4.5. Replication và Sharding
- Sharding:
ScyllaDB tự động phân vùng dữ liệu dựa trên partition key (sensor_id). Mỗi sensor_id sẽ được ánh xạ đến một node trong cluster, giúp phân phối tải ghi và đọc đều trên các node.
- Replication:
Hệ thống cấu hình replication factor (ví dụ 3) để dữ liệu được sao chép trên 3 node khác nhau, đảm bảo tính sẵn sàng và độ bền dữ liệu khi có node bị lỗi.

#### 4.6. Mô hình truy vấn tối ưu
- Truy vấn phổ biến: Lấy dữ liệu cảm biến theo sensor_id và khoảng thời gian (timestamp từ ... đến ...).
- Truy vấn metadata theo sensor_id.
- Truy vấn cảnh báo (alerts) theo sensor_id hoặc alert_type.

### 5. Chiến lược triển khai và cấu hình hệ thống

#### 5.1. Mô hình triển khai tổng quan

Hệ thống được triển khai theo mô hình **microservices container hóa**, với các thành phần được đóng gói bằng Docker, triển khai và điều phối bằng **Docker Compose** (với quy mô nhỏ) hoặc **Kubernetes (K8s)** (khi mở rộng).

#### 5.2. Các thành phần được triển khai
![Bảng ví dụ](/images/bang14.png)

#### 5.3. Docker Compose - ví dụ file `docker-compose.yml`

```yml
version: "3.8"
services:
  mqtt:
    image: eclipse-mosquitto
    ports:
      - "1883:1883"
      - "9001:9001"
    volumes:
      - ./config/mosquitto.conf:/mosquitto/config/mosquitto.conf

  scylladb:
    image: scylladb/scylla
    container_name: scylla-node1
    ports:
      - "9042:9042"  # CQL
    command: --smp 1 --memory 512M

  ingestion:
    build: ./data_ingestion
    depends_on:
      - mqtt
      - scylladb

  api:
    build: ./rest_api
    ports:
      - "5000:5000"
    depends_on:
      - scylladb

  dashboard:
    build: ./frontend
    ports:
      - "3000:3000"
```

#### 5.4. Triển khai bằng Kubernetes (khi mở rộng)

* Dùng **Kubernetes** để quản lý các Pod, Service và Volume nếu cần mở rộng quy mô.
* **ScyllaDB Operator** sẽ giúp tự động tạo cluster ScyllaDB với cấu hình replication, sharding và giám sát hiệu quả.
* Dùng **Helm** để cài đặt Mosquitto, ScyllaDB, các service ingestion/API/frontend nhanh chóng.

#### 5.5. Triển khai thử nghiệm nội bộ

* Cài đặt Docker Desktop và VS Code

* Clone repository dự án

* Chạy lệnh:

  ```bash
  docker-compose up --build
  ```

* Gửi dữ liệu test từ script MQTT publisher (hoặc thiết bị thật).

* Truy cập dashboard tại `http://localhost:5173/`.

#### 5.6. Ưu điểm của chiến lược triển khai

| Ưu điểm                             | Giải thích                                                                          |
| ----------------------------------- | ----------------------------------------------------------------------------------- |
| **Dễ quản lý**                      | Docker Compose giúp định nghĩa toàn bộ kiến trúc trong một file duy nhất            |
| **Tái sử dụng và mở rộng dễ dàng**  | Các thành phần là độc lập, có thể scale theo nhu cầu                                |
| **Tự động hóa và CI/CD**            | Dễ dàng tích hợp vào pipeline CI/CD sau này (GitHub Actions, GitLab CI, Jenkins...) |
| **Chuyển đổi môi trường linh hoạt** | Có thể chuyển đổi từ Docker sang Kubernetes mà không cần viết lại toàn bộ logic     |
