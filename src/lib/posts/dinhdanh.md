---
title: "Định danh"
date: "2025-05-18"
updated: "2025-05-18"
categories:
  - "sveltekit"
  - "markdown"
  - "svelte"
coverImage: "/images/jerry-zhang-ePpaQC2c1xA-unsplash.jpg"
coverWidth: 16
coverHeight: 9
excerpt: This post demonstrates how to include a Svelte component in a Markdown post.
---

### **BÀI TẬP 1: Quy trình duyệt một trang web – Tra cứu DNS, DNS Resolving, DNS Caching**
#### **1. Mô tả quy trình duyệt trang web [www.motvidu.com](http://www.motvidu.com)**
Khi người dùng truy cập vào một trang web như `www.motvidu.com`, quy trình diễn ra như sau:
##### **Bước 1: Trình duyệt kiểm tra cache cục bộ**
* Trình duyệt kiểm tra xem tên miền `www.motvidu.com` đã được phân giải IP chưa trong **DNS cache** nội bộ.
* Nếu có (cache hợp lệ), trình duyệt dùng IP này để gửi HTTP/HTTPS request đến máy chủ đó.
##### **Bước 2: Hệ điều hành kiểm tra cache DNS**
* Nếu trình duyệt không có cache, hệ điều hành sẽ kiểm tra cache DNS toàn hệ thống.
##### **Bước 3: Truy vấn DNS Recursive Resolver**
* Nếu hệ điều hành không tìm thấy, nó gửi yêu cầu đến **DNS Resolver** (thường là của nhà mạng hoặc cấu hình thủ công như 8.8.8.8 của Google).
* Resolver đóng vai trò trung gian đi tra cứu IP của tên miền đó.
##### **Bước 4: DNS Resolver tiến hành phân giải (Resolving)**
* DNS Resolver thực hiện quy trình phân giải như sau:
  1. **Hỏi Root DNS Server**: để biết DNS server của `.com`
  2. **Hỏi Top-Level Domain Server (.com)**: để biết DNS server của `motvidu.com`
  3. **Hỏi Authoritative Name Server**: để nhận được IP thật sự của `www.motvidu.com`
##### **Bước 5: Cache lại kết quả**
* Resolver lưu kết quả phân giải vào cache với thời gian TTL (Time to Live) để các lần sau truy vấn nhanh hơn.
* Hệ điều hành và trình duyệt cũng sẽ lưu cache tạm thời.
##### **Bước 6: Trình duyệt gửi yêu cầu HTTP/HTTPS đến IP**
* Dùng IP nhận được từ DNS, trình duyệt kết nối với máy chủ qua giao thức HTTP hoặc HTTPS.
* Máy chủ phản hồi bằng nội dung trang web.
##### **Bước 7: Hiển thị nội dung**
* Trình duyệt phân tích HTML, CSS, JS... để hiển thị nội dung trang `www.motvidu.com` cho người dùng.

#### **2. Khái niệm cần nhớ**
* **DNS (Domain Name System):** Hệ thống phân giải tên miền thành địa chỉ IP.
* **Resolving DNS:** Quá trình tìm ra IP từ tên miền thông qua chuỗi các máy chủ DNS.
* **Caching:** Lưu trữ tạm thời kết quả phân giải để tăng tốc truy cập lần sau.

#### **3. Nguồn tài liệu tham khảo**
* [Cloudflare – What is DNS?](https://www.cloudflare.com/learning/dns/what-is-dns/)
* [How DNS Works - DigitalOcean](https://www.digitalocean.com/community/tutorials/an-introduction-to-dns-terminology-components-and-concepts)
* [RFC 1035 - Domain Names - Implementation and Specification](https://datatracker.ietf.org/doc/html/rfc1035)

---
### **BÀI TẬP 2: Thuật toán Chord, test case và báo cáo**
#### **1. Giới thiệu Chord**
Chord là một giao thức **phân tán theo vòng tròn (ring-based DHT)** giúp các nút trong mạng tìm kiếm tài nguyên (file, khóa) một cách hiệu quả. Mỗi nút và khóa đều được ánh xạ thành một số trong không gian băm (ID space), thường là modulo `2^m`.

#### **2. Mã nguồn thuật toán Chord đơn giản (Python)**
```python
import hashlib
def hash_key(key, m=5):
    return int(hashlib.sha1(key.encode()).hexdigest(), 16) % (2 ** m)
class Node:
    def __init__(self, node_id, m=5):
        self.id = node_id
        self.m = m
        self.successor = self
        self.finger_table = [None] * m
    def find_successor(self, key_id):
        if self.id < key_id <= self.successor.id:
            return self.successor
        else:
            node = self.closest_preceding_node(key_id)
            return node.find_successor(key_id)
    def closest_preceding_node(self, key_id):
        for i in reversed(range(self.m)):
            if self.finger_table[i] and self.id < self.finger_table[i].id < key_id:
                return self.finger_table[i]
        return self
    def join(self, existing_node):
        if existing_node:
            self.successor = existing_node.find_successor(self.id)
        else:
            self.successor = self  # First node
    def __repr__(self):
        return f"Node({self.id})"
```

#### **3. Test case đơn giản**
```python
def test_chord():
    m = 5  # Không gian khóa 0–31
    node0 = Node(0, m)
    node0.join(None)
    node8 = Node(8, m)
    node8.join(node0)
    node15 = Node(15, m)
    node15.join(node0)
    print("Successor of 3:", node0.find_successor(3))
    print("Successor of 10:", node0.find_successor(10))
    print("Successor of 16:", node0.find_successor(16))
test_chord()
```

#### **4. Ví dụ cụ thể và giải thích**
Giả sử có 3 nút trong hệ thống: Node(0), Node(8), Node(15). Ta tìm successor cho các khóa sau:
* **Khóa 3:** Node(8) là nút gần nhất phía sau 3 ⇒ kết quả là Node(8)
* **Khóa 10:** Node(15) là successor gần nhất sau 10 ⇒ kết quả là Node(15)
* **Khóa 16:** Không có nút nào > 16, nên quay vòng về Node(0) ⇒ kết quả là Node(0)

#### **5. Nguồn tài liệu tham khảo**
* [Chord: A Scalable Peer-to-peer Lookup Protocol](https://pdos.csail.mit.edu/papers/chord:sigcomm01/chord_sigcomm.pdf)
* [MIT 6.824: Distributed Systems](https://pdos.csail.mit.edu/6.824/)
* [Wikipedia – Chord (peer-to-peer)](https://en.wikipedia.org/wiki/Chord_%28peer-to-peer%29)

