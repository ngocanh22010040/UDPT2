---
title: "Ứng dụng phân tán - Bài tập 2"
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

## 1. Kiểm tra CPU, GPU, RAM và giải thích về hiệu năng máy tính

- CPU: Máy của bạn sử dụng Intel Core i5-11320H, 4 lõi, 8 luồng, với tốc độ cơ bản 3.19 GHz và có thể tăng tốc tối đa lên tới 4.4 GHz. Điều này giúp máy xử lý tốt các tác vụ đa nhiệm, chơi game nhẹ, lập trình và xử lý đồ họa cơ bản.

- RAM: Máy của bạn có 16 GB RAM với tốc độ 4267 MT/s, đủ mạnh mẽ để chạy nhiều ứng dụng cùng lúc mà không gặp vấn đề về hiệu suất.

- GPU: Intel Iris Xe Graphics là GPU tích hợp, giúp xử lý các tác vụ đồ họa cơ bản. Tuy nhiên, GPU này không mạnh mẽ như các card đồ họa rời, nên nếu làm việc với đồ họa nặng hoặc chơi game cao cấp, bạn sẽ cần một GPU rời mạnh mẽ hơn.

 Kết luận: Máy của bạn có hiệu suất khá tốt cho các công việc văn phòng, lập trình, chỉnh sửa ảnh/video nhẹ và các tác vụ đa nhiệm. Tuy nhiên, GPU tích hợp sẽ hạn chế khả năng xử lý đồ họa nặng.

## 2. Liệt kê 12 bài toán phổ biến trong ngành CNTT sử dụng đa luồng và đa tiến trình

- 1. Xử lý song song (Parallel Processing):
Tăng tốc các tác vụ tính toán nặng như mô phỏng, phân tích dữ liệu lớn.

- 2. Web Server:
Xử lý nhiều yêu cầu đồng thời từ người dùng.

- 3. Xử lý hình ảnh (Image Processing):
Xử lý nhiều ảnh cùng lúc, mỗi ảnh có thể được xử lý trên một luồng hoặc tiến trình riêng.

- 4. Chia sẻ tài nguyên (Resource Sharing):
Các ứng dụng chia sẻ tài nguyên, như hệ thống cơ sở dữ liệu, yêu cầu quản lý đồng bộ giữa các tiến trình.

- 5. Tải dữ liệu đồng thời (Concurrent Data Loading):
Tải nhiều dữ liệu từ các nguồn khác nhau đồng thời mà không gây tắc nghẽn.

- 6. Game Multiplayer:
Mỗi người chơi là một tiến trình hoặc luồng riêng biệt để xử lý các hành động trong game.

- 7. Mạng P2P (Peer-to-Peer Network):
Quản lý kết nối mạng giữa các nút trong mạng phân tán.

- 8. Chạy thử nghiệm AI (AI Model Training):
Các mô hình AI có thể chạy trên nhiều tiến trình để đào tạo trên các tập dữ liệu lớn.

- 9. Tìm kiếm song song (Parallel Search):
Tìm kiếm thông tin trong cơ sở dữ liệu hoặc tìm kiếm trong không gian dữ liệu rộng lớn.

- 10. Đồng bộ dữ liệu (Data Synchronization):
Đồng bộ hóa dữ liệu giữa các tiến trình hoặc các máy tính khác nhau.

- 11. Ứng dụng tài chính (Financial Modeling):
Các tính toán mô hình tài chính có thể được chia thành nhiều phần và xử lý song song.

- 12. Tải trang web (Web Scraping):
Mỗi trang web hoặc mỗi phần của trang có thể được tải trong một tiến trình riêng biệt.

## 3. Danh sách các trường hợp nên dùng Thread, Process và cả hai
![Bảng ví dụ](/images/bang1.png)

## 4. Tìm hiểu về ChatGPT training với distributed system
ChatGPT sử dụng distributed systems để huấn luyện các mô hình AI của mình với tập dữ liệu cực lớn. Điều này được thực hiện bằng cách phân chia quá trình huấn luyện thành các phần nhỏ và chạy song song trên nhiều máy tính (hoặc node) trong hệ thống phân tán. Cụ thể:

- Parallel Computing: Quá trình huấn luyện được phân chia thành các tác vụ nhỏ và thực hiện đồng thời trên nhiều node trong một cụm máy tính.

- Data Parallelism: Dữ liệu lớn được chia nhỏ và phân phối cho các tiến trình hoặc máy chủ riêng biệt để xử lý.

- Model Parallelism: Các phần của mô hình cũng có thể được phân phối giữa các máy tính khác nhau, giúp tăng tốc độ huấn luyện.

- Dịch vụ đám mây: Việc sử dụng các dịch vụ đám mây như AWS, Google Cloud, hay Azure cho phép các mô hình như GPT được huấn luyện trên một lượng tài nguyên tính toán khổng lồ.
### Tài liệu tham khảo: 
[Truy cập Google Cloud Blog tại đây](https://cloud.google.com/blog)