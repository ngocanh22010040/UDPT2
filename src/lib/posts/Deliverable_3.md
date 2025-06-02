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


#  **BÁO CÁO TIẾN ĐỘ DỰ ÁN**

### 1. Tóm tắt tiến độ dự án

Tính đến thời điểm hiện tại, dự án đã hoàn thành các thành phần chính như sau:

*  **Hoàn thiện giao diện người dùng**: Bao gồm 4 trang chính và một số trang phụ, thiết kế responsive và đơn giản.
*   **Chức năng cơ bản đã hoàn thành**:

  * Giao tiếp giữa các nút và client-server: đổi trang, chọn và lọc sản phẩm.
  * Tìm kiếm sản phẩm phía client (realtime).
  * CORS giữa frontend và backend.
*   **Triển khai tự động hệ thống** với Docker Compose.
*   **API RESTful** quản lý sản phẩm sử dụng FastAPI và ScyllaDB.

#### Những phần đang phát triển:

*   **Fault Tolerance**: Đang xây dựng cơ chế khôi phục khi một nút gặp lỗi (retry logic, failover).
*   **Leader Election**: Đang thử nghiệm thuật toán đơn giản để bầu chọn leader.
*   **Load Balancing**: Dự kiến tích hợp HAProxy hoặc custom round-robin trong giai đoạn tiếp theo.
*   **System Recovery**: Đang thử nghiệm khả năng để các nút tự động gia nhập lại hệ thống sau khi khởi động lại.


### 2. Các thách thức & cách khắc phục
![Bảng ví dụ](/images/bang16.png)

### 3. Các tính năng đã hoàn thành

####   Quản lý dữ liệu sản phẩm với FastAPI + ScyllaDB

* API RESTful: `/shoes` trả về toàn bộ danh sách sản phẩm.
* API chi tiết: `/shoes/{id}` lấy thông tin chi tiết theo UUID.

####   Giao diện và tìm kiếm realtime

* Tìm kiếm sản phẩm bằng JS không cần reload lại trang.
* Giao diện trực quan, phản hồi theo từng phím gõ.

####   Giao tiếp frontend-backend

* Giao tiếp qua CORS và lời gọi HTTP API.
* Kết nối ổn định giữa phần UI và dịch vụ dữ liệu.

####   Docker Compose

* Tự động triển khai hệ thống: FastAPI, ScyllaDB, frontend.
* Khởi động hệ thống với `docker-compose up`.


### 4. Tính năng kỹ thuật đang được thử nghiệm
![Bảng ví dụ](/images/bang17.png)


### 5. Demo hệ thống

####  Hình ảnh:

* Dashboard giám sát trạng thái nút (ảnh đính kèm trong báo cáo PDF).
* Giao diện trang chủ hiển thị danh sách sản phẩm.

####  Video demo:

* Hiển thị quá trình giao tiếp giữa các nút (upload lên blog/Youtube).
* Trình bày việc thêm sản phẩm và đồng bộ dữ liệu.

### 6. Code Snippets tiêu biểu

####  Tìm kiếm sản phẩm:

```html
<script>
  const search = () => {
    const searchbox = document.getElementById("search-input").value.toUpperCase();
    const product = document.querySelectorAll(".hang11");
    const pname = document.getElementsByTagName("h3");
    for (var i = 0; i < pname.length; i++) {
      let match = product[i].getElementsByTagName('h3')[0];
      if (match) {
        let textvalue = match.textContent || match.innerHTML;
        product[i].style.display = (textvalue.toUpperCase().indexOf(searchbox) > -1) ? "" : "none";
      }
    }
  }
</script>
```

####    FastAPI kết nối ScyllaDB:

```python
@app.get("/shoes")
async def get_shoes():
    rows = session.execute("SELECT * FROM shoes LIMIT 100")
    return JSONResponse(content=[{
        "id": str(row.id),
        "name": row.name,
        "brand": row.brand,
        "model": row.model,
        "price": row.price,
        "description": row.description,
        "images": row.images,
        "detail_url": row.detail_url
    } for row in rows])
```
 
### 7. Kế hoạch tiếp theo

*   Tích hợp Load Balancer (dự kiến: HAProxy hoặc thuật toán tùy chỉnh).
*   Hoàn thiện Fault Tolerance + thử nghiệm các lỗi thực tế.
*   Triển khai Leader Election và cơ chế System Recovery.
*   Thực hiện Stress Test với nhiều yêu cầu đồng thời.
*   Viết báo cáo PDF + Video demo.
*   Đảm bảo phân công rõ ràng, commit đầy đủ trên GitHub.
