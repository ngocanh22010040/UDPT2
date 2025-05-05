---
title: "Ứng dụng phân tán - Bài tập 1"
date: "2025-04-28"
updated: "2025-04-28"
categories:
  - sveltekit
  - markdown
  - svelte
coverImage: "/images/anh1.png"
coverWidth: 16
coverHeight: 9
excerpt: "This post demonstrates how to include a Svelte component in a Markdown post."
---

## Định nghĩa 1

 Ứng dụng phân tán  là Hệ thống phân tán (Distributed System) — tập hợp các máy tính độc lập, kết nối với nhau qua mạng, phối hợp để thực hiện một nhiệm vụ chung. Dù các thành phần nằm ở nhiều vị trí khác nhau, hệ thống vẫn cung cấp cho người dùng một giao diện thống nhất và mạch lạc. Điều này giúp tăng cường khả năng mở rộng, độ tin cậy và hiệu suất của hệ thống.

## Ứng dụng của UDPT

-  Scalability (Khả năng mở rộng):  Khả năng của hệ thống tăng cường tài nguyên (như CPU, bộ nhớ) để xử lý tải cao hơn.
-  Fault Tolerance (Khả năng chịu lỗi):  Khả năng của hệ thống duy trì hoạt động bình thường dù một hoặc nhiều thành phần gặp sự cố.
-  Availability (Khả năng sẵn sàng):  Tỷ lệ thời gian hệ thống hoạt động bình thường và có sẵn để phục vụ yêu cầu người dùng.
-  Transparency (Tính minh bạch):  Ẩn đi sự phức tạp của hệ thống phân tán, người dùng và lập trình viên chỉ tương tác với một hệ thống duy nhất.
-  Concurrency (Đồng thời):  Khả năng xử lý nhiều tác vụ đồng thời trên các máy khác nhau.
-  Parallelism (Song song):  Thực thi nhiều tác vụ cùng lúc trên nhiều lõi hoặc máy tính khác nhau để tăng tốc độ xử lý.
-  Openness (Tính mở):  Hệ thống cho phép tích hợp và tương tác với các hệ thống khác một cách dễ dàng.
-  Vertical Scaling (Mở rộng theo chiều dọc):  Tăng cường tài nguyên cho một máy đơn lẻ (ví dụ: nâng cấp CPU, RAM).
-  Horizontal Scaling (Mở rộng theo chiều ngang):  Thêm nhiều máy mới vào hệ thống để phân phối tải.
-  Load Balancer (Cân bằng tải):  Thiết bị hoặc phần mềm phân phối đều yêu cầu đến các máy chủ để tối ưu hiệu suất và tránh quá tải.
-  Replication (Nhân bản):  Tạo bản sao dữ liệu để tăng cường độ tin cậy và khả năng phục hồi của hệ thống.

## Các khái niệm chính của hệ thống phân tán

-  Scalability:  Hệ thống có thể mở rộng bằng cách thêm nhiều máy tính tham gia mạng.
-  Fault Tolerance:  Nếu một máy tính rời mạng, dữ liệu vẫn có thể truy cập từ các máy khác.
-  Availability:  Hệ thống luôn sẵn sàng chia sẻ tệp khi có yêu cầu.
-  Transparency:  Người dùng không cần biết các máy tính khác nhau đang chia sẻ tệp như thế nào.
-  Concurrency:  Nhiều người dùng có thể tải xuống tệp cùng lúc từ các nguồn khác nhau.
-  Parallelism:  Tệp có thể được tải xuống từ nhiều máy cùng lúc để tăng tốc độ.
-  Openness:  Hệ thống cho phép tích hợp với các ứng dụng khác để chia sẻ tệp.
-  Vertical Scaling:  Nâng cấp máy tính để tăng cường khả năng chia sẻ tệp.
-  Horizontal Scaling:  Thêm nhiều máy tính vào mạng để tăng cường khả năng chia sẻ tệp.
-  Load Balancer:  Phân phối yêu cầu tải xuống tệp đến các máy tính khác nhau để tối ưu hiệu suất.
-  Replication:  Tệp được sao chép trên nhiều máy tính để đảm bảo có sẵn khi cần.

## Ví dụ minh họa và liên hệ với các khái niệm

 Ví dụ: Hệ thống Netflix 

-  Scalability:  Netflix thêm máy chủ mới để đáp ứng nhu cầu tăng cao trong các khung giờ cao điểm.
-  Fault Tolerance:  Nếu một máy chủ bị lỗi, người dùng được chuyển sang máy chủ khác mà không gián đoạn dịch vụ.
-  Availability:  Người dùng ở khắp nơi trên thế giới có thể truy cập Netflix gần như mọi lúc.
-  Transparency:  Người dùng không cần biết phim mình xem được stream từ đâu.
-  Concurrency:  Hàng triệu người dùng xem phim cùng lúc.
-  Parallelism:  Các server Netflix xử lý yêu cầu stream của nhiều người dùng đồng thời.
-  Openness:  Netflix cung cấp API để các nhà phát triển bên ngoài tích hợp dịch vụ.
-  Vertical Scaling:  Netflix có thể nâng cấp các server chính với phần cứng mạnh hơn.
-  Horizontal Scaling:  Netflix mở rộng server trên nhiều khu vực địa lý (multi-region).
-  Load Balancer:  Hệ thống cân bằng tải phân phối người dùng tới server gần nhất.
-  Replication:  Nội dung phim được sao lưu trên nhiều server tại nhiều khu vực khác nhau.

## Kiến trúc hệ thống phân tán

Trong hệ thống phân tán, kiến trúc đóng vai trò then chốt để đảm bảo hiệu suất, tính sẵn sàng và khả năng mở rộng. Dưới đây là một số mô hình kiến trúc phổ biến:

1.  Kiến trúc Peer-to-Peer (P2P):   
   Mỗi nút trong mạng vừa là máy khách (client) vừa là máy chủ (server).  
   Các nút chia sẻ tài nguyên trực tiếp với nhau mà không cần máy chủ trung gian.  
   Ứng dụng thực tế: BitTorrent, các mạng chia sẻ tệp P2P khác.

2.  Kiến trúc Microservices:   
   Ứng dụng được chia thành nhiều dịch vụ nhỏ, độc lập.  
   Các dịch vụ giao tiếp với nhau thông qua API.  
   Mỗi dịch vụ có thể được phát triển, triển khai, mở rộng và bảo trì độc lập.  
   Ứng dụng thực tế: Netflix, Amazon, Uber.

3.  Kiến trúc Publish-Subscribe (Pub-Sub):   
   Các thành phần hệ thống phân thành hai vai trò: Nhà xuất bản (Publishers) và Người đăng ký (Subscribers).  
   Publishers gửi thông điệp đến các chủ đề (topics); subscribers nhận thông điệp từ những chủ đề mà họ quan tâm.  
   Giúp giảm sự phụ thuộc giữa các thành phần và tăng khả năng mở rộng.  
   Ứng dụng thực tế: Hệ thống cảnh báo, hệ thống chat real-time.

4.  Client-Server Model:   
   Client gửi yêu cầu, server xử lý yêu cầu và trả kết quả về client.  
   Đây là mô hình cơ bản nhất trong nhiều ứng dụng phân tán hiện nay.

5.  Multi-tier (N-tier) Architecture:   
   Chia hệ thống thành nhiều tầng riêng biệt: Presentation (giao diện), Application (logic xử lý), Data (dữ liệu).  
   Tăng tính phân chia công việc, dễ mở rộng và bảo trì.

6.  Event-Driven Architecture (EDA):   
   Các thành phần giao tiếp với nhau thông qua các sự kiện (events).  
   Khi một sự kiện xảy ra, các thành phần liên quan sẽ phản hồi hoặc thực hiện hành động phù hợp.  
   Ứng dụng thực tế: Hệ thống thanh toán trực tuyến, xử lý đơn hàng thương mại điện tử.

## Xu hướng kiến trúc mới nhất

-  Serverless Architecture:  Triển khai ứng dụng mà không cần quản lý server vật lý.
-  Service Mesh (Istio, Linkerd):  Kiểm soát giao tiếp giữa các service.
-  Edge Computing:  Xử lý dữ liệu tại vị trí gần với người dùng để giảm độ trễ.

## Ví dụ kiến trúc hệ thống phân tán thực tế

 Ví dụ: Amazon Web Services (AWS) 

AWS sử dụng kiến trúc multi-region, mỗi khu vực có nhiều availability zones, giúp đảm bảo:

- Tính sẵn sàng (Availability)
- Khả năng chịu lỗi (Fault Tolerance)
- Khả năng mở rộng linh hoạt (Scalability)

Các dịch vụ như Amazon S3 (lưu trữ đám mây) và EC2 (máy chủ ảo) là những thành phần phân tán chủ chốt trong kiến trúc này.

---

 Tác giả:  Nguyễn Thị Ngọc Anh  
 Ngày đăng:  28/04/2025
