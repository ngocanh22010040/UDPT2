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

# Thiết kế Hệ thống (Deliverable 2)

### 1. **Bản vẽ kiến trúc hệ thống (System Architecture Diagram)**

**Mô tả sơ đồ kiến trúc:**

Hệ thống bao gồm 3 thành phần chính: frontend, backend và cơ sở dữ liệu ScyllaDB, được triển khai thông qua Docker Compose. Người dùng tương tác với giao diện frontend (trình duyệt), các yêu cầu được gửi đến backend (FastAPI), backend kết nối với ScyllaDB để truy xuất hoặc ghi dữ liệu.
![Bảng ví dụ](/images/sd.png)
### 2. **Mô tả chi tiết các thành phần trong hệ thống**

#### 2.1.Frontend (React + JavaScript)
- Vai trò: Giao diện người dùng tương tác trực tiếp với khách hàng, hiển thị danh sách sản phẩm, cung cấp chức năng tìm kiếm, lọc sản phẩm theo thời gian thực mà không cần tải lại trang.
- Cách thức hoạt động: Gửi yêu cầu HTTP đến backend API, nhận dữ liệu JSON trả về để cập nhật giao diện động. Xử lý các sự kiện người dùng (như gõ từ khóa tìm kiếm) ngay trên trình duyệt.

#### 2.2.Backend REST API (FastAPI)
- Vai trò: Xử lý các yêu cầu từ frontend, thực hiện nghiệp vụ quản lý dữ liệu sản phẩm, giao tiếp với cơ sở dữ liệu, cung cấp dữ liệu trả về theo chuẩn JSON.
- Cách thức hoạt động: Nhận request HTTP, gọi tới cơ sở dữ liệu ScyllaDB để thực hiện thao tác truy vấn, thêm, sửa, xóa sản phẩm; sau đó trả kết quả về frontend. Hỗ trợ CORS để cho phép frontend truy cập API dễ dàng.

#### 2.3.Cơ sở dữ liệu (ScyllaDB Cluster)
- Vai trò: Lưu trữ dữ liệu sản phẩm giày với khả năng xử lý nhanh, mở rộng tốt và độ tin cậy cao.
- Cách thức hoạt động:
 -  Sharding: Dữ liệu được phân tán (chia nhỏ) theo khóa chính (UUID sản phẩm) dựa trên thuật toán phân vùng (partitioning) của ScyllaDB. Mỗi node trong cluster lưu một phần dữ liệu, giúp tăng khả năng xử lý song song và giảm tải cho từng node.
 -  Replication: Mỗi bản ghi được sao chép nhiều lần (replication factor) trên các node khác nhau để đảm bảo tính sẵn sàng và khả năng chống mất dữ liệu khi một node gặp lỗi. Khi có thay đổi, dữ liệu được đồng bộ giữa các bản sao theo cơ chế nhất quán cuối (eventual consistency).
 -  Thuật toán phân phối: ScyllaDB sử dụng consistent hashing để định vị dữ liệu đến node phù hợp dựa trên partition key.

#### 2.4.Load Balancer
- Vai trò: Phân phối các yêu cầu từ frontend đến nhiều instance backend API nhằm cân bằng tải và tăng khả năng chịu lỗi.
- Cách thức hoạt động: Dựa trên thuật toán round-robin hoặc các thuật toán cân bằng tải khác, load balancer chuyển tiếp yêu cầu tới backend đang sẵn sàng và ít tải.

#### 2.5. Leader Election Module
- Vai trò: Quản lý việc chọn một node làm leader trong cụm backend nhằm đảm bảo tính nhất quán trong các hoạt động phối hợp hoặc đồng bộ.
- Cách thức hoạt động: Sử dụng thuật toán bầu chọn leader phổ biến như Bully hoặc Raft, các node sẽ tranh chọn leader dựa trên tiêu chí như ưu tiên ID hoặc trạng thái hoạt động, đảm bảo luôn có một node làm đầu mối xử lý các tác vụ quan trọng.

#### 2.6.Fault Tolerance Module
- Vai trò: Giám sát hoạt động của các node, xử lý các sự cố như node bị lỗi hoặc mất kết nối để hệ thống vẫn hoạt động ổn định.
- Cách thức hoạt động: Kiểm tra định kỳ trạng thái các node, tự động retry kết nối khi có lỗi, và khởi động lại node hoặc chuyển sang node dự phòng khi phát hiện sự cố, tránh làm gián đoạn dịch vụ.

#### 2.7.Docker Compose
- Vai trò: Công cụ quản lý và tự động hóa việc triển khai các thành phần hệ thống (frontend, backend, database, các module hỗ trợ) trên môi trường phát triển hoặc sản xuất.
- Cách thức hoạt động: Định nghĩa các service trong file docker-compose.yml, thiết lập mạng nội bộ và các biến môi trường cần thiết, giúp dễ dàng khởi động và cấu hình đồng thời tất cả các thành phần hệ thống bằng một lệnh duy nhất.


### 3. **Công nghệ và thư viện sử dụng**
![Bảng ví dụ](/images/bang15.png)

### 4. **Mô hình dữ liệu (Database Model)**
#### Mô hình bảng `shoes` trong ScyllaDB

| Tên trường    | Kiểu dữ liệu       | Mô tả                                   |
| ------------- | ------------------ | --------------------------------------- |
| `id`          | UUID (PRIMARY KEY) | Khóa chính, định danh duy nhất sản phẩm |
| `name`        | text               | Tên sản phẩm                            |
| `brand`       | text               | Thương hiệu sản phẩm                    |
| `model`       | text               | Mẫu sản phẩm                            |
| `price`       | double             | Giá bán                                 |
| `description` | text               | Mô tả chi tiết sản phẩm                 |
| `images`      | list<text>         | Danh sách URL ảnh sản phẩm              |
| `detail_url`  | text               | Đường dẫn trang chi tiết sản phẩm       |

* **UUID làm khóa chính** giúp định danh duy nhất và phân phối dữ liệu đều trên các node trong cluster theo cơ chế consistent hashing.
* **Sharding tự động:** ScyllaDB tự động phân chia dữ liệu theo `id` trên nhiều node để tối ưu truy vấn song song và hiệu năng.
* **Replication:** Dữ liệu được sao chép trên nhiều node nhằm đảm bảo tính sẵn sàng và dự phòng khi node bị lỗi.
* **Thiết kế bảng tối ưu cho việc đọc và ghi nhanh**, phù hợp với hệ thống có lượng lớn sản phẩm và yêu cầu truy vấn nhanh theo ID hoặc danh sách sản phẩm.

### 5. Chiến lược triển khai và cấu hình hệ thống

#### Công cụ triển khai:

* **Docker & Docker Compose** được sử dụng để xây dựng, đóng gói và triển khai các thành phần của hệ thống.
* Mỗi thành phần (frontend, backend, ScyllaDB cluster) được đóng gói trong container riêng biệt.
* Docker Compose dùng để quản lý đa container, thiết lập mạng nội bộ giữa các service, biến môi trường, volume lưu trữ dữ liệu.

#### Quy trình triển khai:

1. **Khởi tạo môi trường:**

   * Sử dụng Docker Compose file (`docker-compose.yml`) định nghĩa các service:

     * `frontend` (React app)
     * `backend` (FastAPI)
     * `scylla` (cụm ScyllaDB)
     * Có thể thêm `load_balancer`, `leader_election`, và các service hỗ trợ khác.

2. **Cấu hình mạng nội bộ:**

   * Các container được kết nối qua mạng bridge riêng biệt để đảm bảo an toàn và hiệu suất truyền thông.

3. **Thiết lập volume:**

   * Lưu trữ dữ liệu ScyllaDB trên volume persist để tránh mất dữ liệu khi container khởi động lại.

4. **Khởi động và đồng bộ:**

   * Backend có cơ chế chờ ScyllaDB sẵn sàng trước khi kết nối (wait-for-port logic).
   * Các service frontend và backend khởi động song song, đảm bảo kết nối API mượt mà.

5. **Quản lý lỗi và tự phục hồi:**

   * Docker Compose cấu hình restart policy để tự động khởi động lại container khi gặp sự cố.
   * Phần backend và cơ sở dữ liệu có cơ chế retry, leader election để đảm bảo fault tolerance.

#### Kế hoạch mở rộng:
* Trong tương lai, có thể mở rộng triển khai bằng **Kubernetes** để tận dụng:

  * Tự động scaling (Horizontal Pod Autoscaling)
  * Quản lý cấu hình (ConfigMap, Secrets)
  * Load balancing nội bộ (Service)
  * Quản lý trạng thái (StatefulSets) cho cụm ScyllaDB
* Kubernetes giúp tăng độ bền vững, tự phục hồi và dễ dàng quản lý hệ thống lớn hơn khi dự án phát triển.

