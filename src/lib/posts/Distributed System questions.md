---
title: "Buổi 6"
date: "2025-05-26"
updated: "2025-05-26"
categories:
  - "sveltekit"
  - "markdown"
  - "svelte"
coverImage: "/images/jerry-zhang-ePpaQC2c1xA-unsplash.jpg"
coverWidth: 16
coverHeight: 9
excerpt: This post demonstrates how to include a Svelte component in a Markdown post.
---

## Distributed System questions
### 1. Hệ thống phân tán, tập trung, phi tập trung khác nhau như thế nào, nêu ví dụ về mỗi loại,làm thế nào để xác định sự khác biệt chính?
Hệ thống tập trung (Centralized System):
- Mô tả: Tất cả xử lý và lưu trữ dữ liệu đều tập trung ở một máy chủ duy nhất.
- Ví dụ: Máy tính mainframe, hệ thống quản lý ngân hàng cổ điển.

Hệ thống phi tập trung (Decentralized System):
- Mô tả: Có nhiều trung tâm điều khiển, không có một điểm trung tâm nào kiểm soát toàn bộ.
- Ví dụ: Blockchain, BitTorrent.

Hệ thống phân tán (Distributed System):
- Mô tả: Tập hợp các máy tính độc lập liên kết với nhau để cùng giải quyết một nhiệm vụ như thể là một hệ thống duy nhất.
- Ví dụ: Google Search, Hệ thống ngân hàng hiện đại.

 Sự khác biệt chính:
- Tập trung: 1 điểm điều khiển duy nhất.
- Phân tán: Nhiều máy hoạt động cùng nhau nhưng có thể điều phối.
- Phi tập trung: Không có điều phối trung tâm, các nút ngang hàng.

### 2. Các đặc tính của hệ phân tán là gì? giải thích cho từng đặc điểm thật kỹ (tìm trong slides)
- Tính minh bạch (Transparency):
Che giấu sự phức tạp, ví dụ như che giấu vị trí, phân mảnh, lỗi...
- Tính mở (Openness):
Hệ thống dễ dàng mở rộng và tương thích với chuẩn công nghiệp.
- Khả năng mở rộng (Scalability):
Có thể mở rộng về kích thước, địa lý, và quản lý.
- Tính sai hỏng (Fault Tolerance):
Hệ thống vẫn hoạt động ngay cả khi một số phần tử bị lỗi.
- Tính đồng thời (Concurrency):
Nhiều người dùng truy cập và xử lý tài nguyên đồng thời.
- Tính độc lập vị trí (Location Transparency):
Người dùng không cần biết dịch vụ hoặc dữ liệu ở đâu.

### 3. Mục đích của nút chủ trong một hệ phân tán là để làm gì? Điều gì sẽ xảy ra nếu nút chủ gặp sự cố?
 - Mục đích:
Điều phối hoạt động, phân công công việc, đồng bộ dữ liệu, duy trì trạng thái chung.
 - Khi bị lỗi:
Gây gián đoạn hệ thống nếu không có cơ chế dự phòng hoặc bầu lại (vd: Bully, Ring).

### 4. Trong một mạng không gian, tại sao các máy thường giao tiếp với nhau qua gossip protocol mà không gửi thông tin đến tất cả các máy khác hoặc gửi về một nút trung tâm
- Lý do: Tiết kiệm băng thông, tăng khả năng mở rộng và tránh quá tải nút trung tâm.
- Không gửi broadcast: Broadcast gây nghẽn mạng và không hiệu quả trong mạng lớn.
- Không gửi về trung tâm: Trung tâm có thể là điểm nghẽn hoặc bị lỗi.

### 5. Các yếu tố cốt lõi của một hệ phân tán là gì
- Tập hợp các nút (nodes) độc lập.
- Mạng truyền thông để các nút kết nối.
- Đồng bộ thời gian và đồng thuận.
- Giao tiếp giữa các tiến trình.
- Quản lý lỗi và phân bổ tài nguyên.

### 6. Nêu những lí do sử dụng hệ phân tán
- Hiệu suất cao hơn, khả năng mở rộng tốt.
- Độ sẵn sàng cao, giảm rủi ro khi một phần bị lỗi.
- Tối ưu chi phí và tận dụng tài nguyên phân tán.
- Khả năng phục vụ toàn cầu.

### 7. Nêu định nghĩa hệ phân tán
Hệ phân tán là tập hợp các máy tính độc lập, người dùng thấy chúng như một hệ thống thống nhất duy nhất.

### 8. Chụp và để lên blog các hình của thuật toán Cristian, Berkeley, RBS, Lamport, Bully, Ring
![Bảng ví dụ](/images/bang5.png)

### 9. Kỹ thuật phân tán nào hỗ trợ lập trình thủ tục, lập trình web, hướng đối tượng, liệt kê
![Bảng ví dụ](/images/bang6.png)

### 10. Tiến trình nhẹ, tiến trình, luồng có những ưu điểm và nhược điểm gì, liệt kê. Khi một lời gọi hệ thống dừng thì đối với 3 loại như thế nào. Tiến trình nhẹ, tiến trình và luồng có mối quan hệ như thế nào với nhau và với chính nó?
![Bảng ví dụ](/images/bang7.png)
Quan hệ:
- Luồng là thành phần con trong tiến trình.
- Tiến trình nhẹ thường là khái niệm tương đương với luồng (ở mức hệ điều hành).

### 11. Mô hình client server là gì, vai trò của máy chủ và máy khách là gì.
- Mô hình Client-Server: Máy khách (client) gửi yêu cầu, máy chủ (server) xử lý và phản hồi.
- Vai trò:
  -- Client: Giao diện người dùng, gửi yêu cầu.
  -- Server: Lưu trữ, xử lý, và phản hồi dữ liệu.

### 12. Quá trình gọi thủ tục từ xa là gì
Mô tả:
- Một tiến trình gọi hàm trên tiến trình khác như thể là nội bộ.
- Dữ liệu gửi qua mạng, có stub, marshal/unmarshal, giao thức truyền thông.

### 13. Nêu mục đích và lợi ích của mô hình phân tầng giao thức, hướng thông điệp bền vững
- Mục đích: Phân chia trách nhiệm rõ ràng, dễ bảo trì và mở rộng.
- Lợi ích:
  - Giảm phức tạp
  - Tăng tính tương thích
  - Tăng độ bền (reliable message delivery)

### 14. Sharding là gì
Sharding: Kỹ thuật chia nhỏ cơ sở dữ liệu thành nhiều mảnh (shard) để cải thiện hiệu năng và khả năng mở rộng.
 - Mỗi shard chứa một phần dữ liệu.

### 15. Các gói luồng có thế làm những nhiệm vụ gì?
- Xử lý song song các tác vụ.
- Tăng hiệu năng xử lý đa nhân.
- Chạy nền, xử lý sự kiện, đồng bộ hóa.
- Ví dụ: Web server xử lý nhiều client song song.

### 16. Phân loại sự khác nhau giữa luồng kiểu người dùng và luồng kiểu nhân
![Bảng ví dụ](/images/bang8.png)

### 17. Nêu các hàm chính ở trong RPC và giải thích chức năng của nó
![Bảng ví dụ](/images/bang10.png)

### 18. Định nghĩa tiến trình, thread, multithread client, multithread server
- Process (Tiến trình): Đơn vị thực thi độc lập, có không gian bộ nhớ riêng.
- Thread (Luồng): Đơn vị nhỏ hơn process, chia sẻ tài nguyên chung.
- Multithreaded Client: Nhiều luồng cùng gửi yêu cầu đến server → tăng hiệu năng, phản hồi tốt hơn.
- Multithreaded Server: Server tạo nhiều luồng để xử lý nhiều client cùng lúc.

### 19. Review lại kiến thức căn bản của Map, Reduce và mục đích trong một hệ phân tán
- Map: Chia nhỏ dữ liệu, xử lý song song → tạo cặp key-value.
- Reduce: Tổng hợp các cặp key-value thành kết quả cuối cùng.
- Mục đích: Xử lý tập dữ liệu lớn, tận dụng khả năng phân tán (Hadoop, Spark…).

### 20. Ảo hóa (Virtulization) là gì, mục đích của ảo hóa trong một hệ phân tán dùng để làm gì?
Định nghĩa: Tạo nhiều máy ảo trên một phần cứng thực.
- Mục đích trong hệ phân tán:
- Chạy nhiều ứng dụng/OS trên cùng máy
- Tăng hiệu quả tài nguyên
- Hỗ trợ mở rộng, backup, rollback dễ dàng

### 21. Review lại các kiến trúc của server đa luồng
![Bảng ví dụ](/images/bang11.png)

### 22. Review lại các hướng tiếp cận cài đặt luồng
- User-level Thread (ULT): Cài bằng thư viện, nhanh nhưng không dùng nhiều CPU lõi.
- Kernel-level Thread (KLT): OS quản lý, dùng được đa lõi nhưng tốn tài nguyên.
- Hybrid approach: Kết hợp cả hai, tối ưu hiệu năng và quản lý.

### 23. Tại sao chúng ta sử dụng bảng băm phân tán (review slides), mục đích của bảng băm phân tán là gì, Consitent Hashing là gì, Finger table để làm gì, tại sao sử dụng finger table
- Mục đích DHT: Phân phối và tra cứu dữ liệu hiệu quả, không cần server trung tâm.
- Consistent Hashing: Phân phối khóa lên vòng tròn → tránh phân mảnh khi thêm/bớt node.
- Finger Table (trong Chord):
  - Mỗi node lưu vị trí các node khác cách mình 2^i
  - Giảm độ trễ tra cứu từ O(n) → O(log n)

### 24. Không gian phẳng là gì, định danh là gì, liệt kê các đặc điểm của không gian phẳng
- Không gian phẳng: Mỗi node/đối tượng có định danh duy nhất, không có phân cấp.
- Đặc điểm:
 - Không phân biệt vùng địa lý
 - Tra cứu khó khăn hơn nếu không có cấu trúc
 - Mỗi định danh là duy nhất (ID, UUID…)

### 25. Tại sao cần đồng bộ hóa đồng hồ logic, tại sao đồng hồ vật lý không đảm bảo. mục đích của đồng bộ hóa và các thuật toán đồng bộ hóa là gì
Đồng hồ vật lý không đảm bảo:
- Trôi giờ
- Không có độ chính xác tuyệt đối giữa các máy

Đồng hồ logic cần thiết để:
- Xác định thứ tự sự kiện (causal order)
- Hỗ trợ các thuật toán phân tán (giao dịch, ghi log…)

### 26. Đồng hồ Lamport giải quyết vấn đề gì, nêu và giải thích các rules của đồng hồ Lamport
- Vấn đề giải quyết: Đảm bảo thứ tự các sự kiện trong hệ phân tán.
- Quy tắc:
 - Mỗi sự kiện nội bộ tăng đồng hồ thêm 1.
 - Khi gửi message, gửi kèm đồng hồ hiện tại.
 - Khi nhận message, đồng hồ = max(local, received) + 1

### 27. Tham khảo lại các bài tập của đồng hồ logic Lamport trong slides chương 4-5

### 28. Giao thức đồng bộ NTP là gì, PTP là gì, được tính toán như thế nào
- NTP (Network Time Protocol): Đồng bộ thời gian giữa client và server. Độ chính xác khoảng vài mili giây.
- PTP (Precision Time Protocol): Độ chính xác cao hơn (micro giây), dùng trong môi trường cần độ chính xác rất cao (như tài chính, công nghiệp).
- Tính toán:
    Sử dụng timestamp gửi/nhận và ước lượng độ trễ.

### 29. Review lại python cơ bản
- Biến, kiểu dữ liệu: int, float, str, list, dict, set…
- Cấu trúc điều khiển: if-else, for, while
- Hàm: def, tham số, trả về
- Lập trình hướng đối tượng: class, object, kế thừa
- Thư viện phổ biến: os, math, random, socket, threading

