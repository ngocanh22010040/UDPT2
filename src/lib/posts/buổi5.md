---
title: "Buổi 5"
date: "2025-05-19"
updated: "2025-05-19"
categories:
  - "sveltekit"
  - "markdown"
  - "svelte"
coverImage: "/images/jerry-zhang-ePpaQC2c1xA-unsplash.jpg"
coverWidth: 16
coverHeight: 9
excerpt: This post demonstrates how to include a Svelte component in a Markdown post.
---


### **Buổi 5 – Notes 7: Các kiến trúc trong Hệ thống phân tán (HPT)**

#### 🔹 **1. Tổng quan về loại trừ lẫn nhau trong Hệ thống phân tán**
Trong hệ thống phân tán (HPT), **vấn đề loại trừ lẫn nhau (Mutual Exclusion)** là đảm bảo rằng tại một thời điểm chỉ **một tiến trình** được phép truy cập vào **vùng tới hạn (Critical Section – CS)**, ví dụ như một tài nguyên dùng chung (file, máy in, cơ sở dữ liệu,...). Không giống như hệ thống tập trung, trong HPT, các tiến trình nằm trên các máy khác nhau và giao tiếp qua mạng, do đó cần có cơ chế đảm bảo đồng bộ giữa các tiến trình.

#### 🔹 **2. Yêu cầu của một thuật toán loại trừ lẫn nhau**
Một thuật toán loại trừ lẫn nhau hiệu quả cần đảm bảo các điều kiện sau:
1. **Mutual Exclusion (Loại trừ lẫn nhau)**: Không có hai tiến trình nào cùng vào CS tại cùng thời điểm.
2. **Freedom from Deadlock (Không bị deadlock)**: Không có vòng chờ vô tận giữa các tiến trình.
3. **Freedom from Starvation (Không bị bỏ đói)**: Mỗi tiến trình đều có cơ hội truy cập CS sau một thời gian hữu hạn.
4. **Fairness (Công bằng)**: Các yêu cầu được xử lý theo thứ tự đúng.
5. **Fault Tolerance (Chịu lỗi)**: Hệ thống vẫn hoạt động tốt nếu có một số thành phần bị lỗi.

#### 🔹 **3. Các kiến trúc thuật toán loại trừ lẫn nhau**
#####  **3.1 Centralized Algorithm (Thuật toán trung tâm)**
* **Cách hoạt động:**
  Một tiến trình trung tâm (coordinator) sẽ điều phối quyền truy cập vào CS. Các tiến trình gửi yêu cầu đến coordinator và chờ phản hồi cho phép vào CS.

* **Quy trình:**
  1. Tiến trình gửi "request" đến coordinator.
  2. Coordinator trả lời "grant" nếu CS trống.
  3. Sau khi xong, tiến trình gửi "release" để trả lại quyền.

* **Đặc điểm:**
  * **Số thông điệp:** 3 (request → grant → release).
  * **Độ trễ vào CS:** 2 message delays.
  * **Chịu lỗi:** Không (nếu coordinator lỗi, hệ thống dừng).
  * **Thứ tự yêu cầu:** Đảm bảo.
  * **Ưu điểm:** Đơn giản, hiệu quả.
  * **Nhược điểm:** Điểm nghẽn đơn (single point of failure).

#####  **3.2 Distributed Algorithm (Thuật toán phân tán – Ricart & Agrawala)**
* **Cách hoạt động:**
  Mỗi tiến trình gửi yêu cầu đến **tất cả các tiến trình khác**, chờ phản hồi từ tất cả để vào CS.

* **Quy trình:**
  1. Tiến trình gửi REQUEST đến (n−1) tiến trình còn lại.
  2. Chờ tất cả REPLY.
  3. Sau khi xong gửi RELEASE (nếu cần).

* **Đặc điểm:**
  * **Số thông điệp:** 2(n−1) (một lượt request và một lượt reply).
  * **Độ trễ vào CS:** 2(n−1) message delays.
  * **Chịu lỗi:** Tốt hơn vì không có node trung tâm.
  * **Thứ tự yêu cầu:** Đảm bảo (nhờ timestamp).
  * **Ưu điểm:** Không điểm nghẽn đơn.
  * **Nhược điểm:** Nhiều thông điệp, khó mở rộng khi số tiến trình lớn.

#####  **3.3 Token Ring Algorithm (Thuật toán vòng token)**
* **Cách hoạt động:**
  Các tiến trình được liên kết logic thành một vòng khép kín. Một **token** duy nhất được chuyền vòng. Chỉ tiến trình giữ token mới được vào CS.

* **Quy trình:**
  1. Token di chuyển theo vòng.
  2. Tiến trình nào giữ token có thể vào CS.
  3. Sau khi xong, truyền token đi.

* **Đặc điểm:**
  * **Số thông điệp:** Tối đa (n−1) để chuyển token.
  * **Độ trễ vào CS:** Tối đa (n−1) message delays.
  * **Chịu lỗi:** Tốt nếu có cơ chế phát hiện/mạo tạo lại token.
  * **Thứ tự yêu cầu:** Không đảm bảo theo thứ tự đến.
  * **Ưu điểm:** Ít thông điệp hơn thuật toán phân tán.
  * **Nhược điểm:** Nếu mất token hoặc node treo, hệ thống phải khôi phục.

#### 🔹 **4. So sánh tổng quát**
![Bảng ví dụ](/images/bang5.png)

#### 🔹 **5. Kết luận**
Việc lựa chọn thuật toán loại trừ lẫn nhau phù hợp phụ thuộc vào **mục tiêu hệ thống**:
* Nếu cần **hiệu quả đơn giản**, ít tiến trình → dùng **Centralized**.
* Nếu cần **phân tán hoàn toàn**, không phụ thuộc node trung tâm → dùng **Distributed**.
* Nếu cần giảm **thông điệp và xung đột**, hệ thống vòng ổn định → dùng **Token Ring**.

