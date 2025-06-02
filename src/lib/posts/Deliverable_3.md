---
title: "Deliverable 3 : Website"
date: "2025-06-1"
updated: "2025-06-1"
categories:
  - "sveltekit"
  - "markdown"
  - "svelte"
coverImage: "/images/jerry-zhang-ePpaQC2c1xA-unsplash.jpg"
coverWidth: 16
coverHeight: 9
excerpt: This post demonstrates how to include a Svelte component in a Markdown post.
---

# Tóm tắt tiến độ dự án

### 1. Các tính năng chính đã triển khai
* **Giao tiếp giữa các nút:** Hệ thống đã hoàn thiện việc kết nối và trao đổi dữ liệu giữa các thành phần backend qua API.
* **Chia sẻ và đồng bộ dữ liệu:** Triển khai thành công cơ chế chia sẻ dữ liệu sản phẩm giữa các nút, đảm bảo nhất quán với cơ chế retry khi lỗi mạng xảy ra.
* **Replication dữ liệu:** Dữ liệu sản phẩm được sao chép giữa các node trong cụm ScyllaDB, đảm bảo tính sẵn sàng và dự phòng.
* **Tìm kiếm sản phẩm:** Tính năng tìm kiếm sản phẩm theo tên được tích hợp ngay trên giao diện frontend với phản hồi thời gian thực.
* **Triển khai tự động bằng Docker Compose:** Đã hoàn thiện việc đóng gói và triển khai các thành phần của hệ thống qua Docker Compose giúp dễ dàng khởi động và quản lý.

### 2. Phần đã hoàn thành và đang phát triển

* **Đã hoàn thành:**
  * Giao diện frontend với các trang chính và các tính năng cơ bản.
  * Backend API quản lý dữ liệu sản phẩm.
  * Cơ chế kết nối và đồng bộ dữ liệu với ScyllaDB.
  * Deployment Automation bằng Docker Compose.

* **Đang phát triển:**
  * Cơ chế Fault Tolerance, đảm bảo hệ thống tiếp tục hoạt động khi một node lỗi.
  * Leader Election để quản lý phân phối vai trò trong cụm.
  * Load Balancing để cân bằng tải yêu cầu đến các node.
  * System Recovery cho phép node lỗi tự phục hồi và tham gia lại cụm.

### 3. Thách thức và giải pháp
* **Đồng bộ dữ liệu khi mạng kém:** Gặp khó khăn trong việc duy trì dữ liệu nhất quán khi mất kết nối, đã giải quyết bằng kỹ thuật retry và kiểm tra nhất quán dữ liệu.
* **Cân bằng tải chưa tối ưu:** Thuật toán phân phối yêu cầu hiện tại còn đơn giản, cần cải tiến để đảm bảo tải đều giữa các node.
* **Tính sẵn sàng và fault tolerance:** Phát triển cơ chế leader election và tự phục hồi để tăng khả năng chịu lỗi.

### 4. Demo hoạt động của hệ thống
* **Ảnh chụp màn hình:** Giao diện dashboard quản lý sản phẩm với chức năng tìm kiếm và hiển thị danh sách.
* **Video minh họa:** Giao tiếp và đồng bộ dữ liệu giữa các node backend, thể hiện tính nhất quán dữ liệu khi một node bị tắt/mở lại.
* Có thể chèn video hoặc ảnh vào website dự án hoặc tài liệu báo cáo.

### 5. Mã nguồn (Code Snippets)

```javascript
// Tìm kiếm sản phẩm realtime trên frontend
const search = () => {
    const searchbox = document.getElementById("search-input").value.toUpperCase();
    const product = document.querySelectorAll(".product-item");
    for(let i = 0; i < product.length; i++){
        let pname = product[i].getElementsByTagName('h3')[0];
        if(pname && pname.textContent.toUpperCase().indexOf(searchbox) > -1){
            product[i].style.display = "";
        } else {
            product[i].style.display = "none";
        }
    }
}
```

```python
# Backend API lấy danh sách sản phẩm từ ScyllaDB
@app.get("/shoes")
async def get_shoes():
    rows = session.execute("SELECT * FROM shoes LIMIT 100")
    return [{
        "id": str(row.id),
        "name": row.name,
        "brand": row.brand,
        "price": row.price
    } for row in rows]
```

### 6. Danh sách tính năng đã hoàn thành
* Giao tiếp phân tán giữa các node backend.
* Chia sẻ và đồng bộ dữ liệu sản phẩm.
* Tìm kiếm sản phẩm trên frontend.
* Cơ chế retry và kiểm tra nhất quán dữ liệu.
* Triển khai tự động bằng Docker Compose.

### 7. Kế hoạch tiếp theo
* Hoàn thiện cơ chế **Fault Tolerance** để hệ thống hoạt động ổn định khi gặp sự cố.
* Phát triển **Leader Election** giúp quản lý vai trò và tránh xung đột.
* Tích hợp **Load Balancing** để phân phối yêu cầu hiệu quả.
* Thử nghiệm và hoàn thiện **System Recovery** để node lỗi có thể tự động tham gia lại cụm.
* Cải tiến thuật toán phân phối dữ liệu và tối ưu hiệu suất hệ thống.

