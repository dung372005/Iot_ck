# HỆ THỐNG QUẢN LÝ CHĂM SÓC THÚ CƯNG THÔNG MINH

## Bài toán

### Nhu cầu thực tế

Việc chăm sóc thú cưng tại các trạm thú y hoặc khách sạn thú cưng hiện nay đòi hỏi sự giám sát liên tục và tiêu tốn nhiều nhân lực. Người quản lý phải theo dõi thủ công tình trạng trống/đầy của từng chuồng, thời điểm cần cho ăn, cũng như chất lượng môi trường không khí (để dọn dẹp kịp thời). Việc quản lý thủ công này dễ dẫn đến thiếu sót, làm giảm chất lượng dịch vụ và ảnh hưởng đến sức khỏe của vật nuôi. Những vấn đề này cho thấy sự cần thiết của một hệ thống quản lý chăm sóc thú cưng thông minh đa chức năng:

*   **Quản lý không gian chuồng tối ưu:** Giúp nhân viên dễ dàng xác định chuồng nào đang trống, chuồng nào có thú cưng để sắp xếp hợp lý.
*   **Tự động hóa chăm sóc:** Tự động phát hiện khi thú cưng tiến đến máng ăn và xả lượng thức ăn phù hợp, giảm bớt công việc thủ công.
*   **Giám sát môi trường & cảnh báo an toàn:** Theo dõi nhiệt độ, độ ẩm tổng thể và phát hiện mùi hôi/chất thải (khí NH3) để đưa ra cảnh báo dọn dẹp kịp thời.

### Nguồn cảm hứng cho dự án

*   **Các hệ thống đỗ xe thông minh:** Dự án lấy cảm hứng từ logic quản lý slot (vị trí) của bãi đỗ xe thông minh, biến tấu thành việc quản lý độc lập từng không gian chuồng thú y, kết hợp với các cơ cấu chấp hành (bơm, quạt, đèn) để tạo thành vòng lặp khép kín.
*   **Nhu cầu nâng cao chất lượng dịch vụ thú y:** Phát triển một mô hình đơn giản, chi phí thấp với ESP32 nhưng có thể mở rộng, giúp các cơ sở chăm sóc quy mô nhỏ dễ dàng tiếp cận công nghệ IoT.

---

## Nội dung chính

### 1. Phần cứng

*   **Vi điều khiển:** ESP32 DevKit V1 (30 chân)
*   **Module nguồn:** XL4015 (Hạ áp ổn định 5V cho cụm động cơ và cảm biến khí)
*   **Khu vực giám sát chuồng:**
    *   2 x Cảm biến siêu âm HC-SR04: Phát hiện sự hiện diện của thú cưng tại 2 chuồng độc lập.
*   **Cổng vào/ra (Cơ cấu xả thức ăn):**
    *   2 x Cảm biến hồng ngoại (IR Sensor): Nhận diện thú cưng khi tiến gần khay ăn.
    *   2 x Động cơ Servo SG90: Mở nắp xả thức ăn tự động khi có tín hiệu từ cảm biến IR.
*   **Hệ thống môi trường và an toàn:**
    *   1 x Cảm biến DHT11: Giám sát nhiệt độ và độ ẩm không khí tổng thể.
    *   1 x Cảm biến MQ (VD: MQ-135/MQ-2): Lắp tại vách ngăn thông gió sát trần để phát hiện mùi hôi/chất lượng không khí chung của 2 chuồng.
    *   4 x Đèn LED (2mm): Bố trí mỗi chuồng 2 đèn, đấu song song chung 1 chân GPIO qua điện trở hạn dòng.
    *   2 x Module Relay (kèm Quạt 5V): Điều khiển thông gió độc lập cho mỗi chuồng.

### 2. Phần mềm

*   **Môi trường phát triển:** PlatformIO (Viết mã C/C++ non-blocking).
*   **Giao thức truyền thông:** MQTT thông qua Broker trực tuyến (ví dụ: EMQX, HiveMQ).
*   **Bảng điều khiển (Node-RED Dashboard):**
    *   **Hiển thị trạng thái chuồng (3 mức cảnh báo):** Màu Xanh (Trống), Màu Đỏ (Có thú cưng), Màu Vàng (Cảnh báo chất lượng không khí kém, cần dọn dẹp).
    *   **Giám sát môi trường:** Hiển thị trực quan Nhiệt độ, Độ ẩm bằng Gauge và vẽ biểu đồ Line Chart theo thời gian thực.
    *   **Điều khiển thủ công:** Cung cấp các công tắc (Switch) để bật/tắt quạt thông gió và đèn LED chiếu sáng độc lập cho từng chuồng.

### 3. Ý tưởng hoạt động

*   **Giám sát hiện diện:** Các cảm biến siêu âm HC-SR04 liên tục đo khoảng cách. Khi có vật nuôi, dữ liệu được ESP32 cập nhật lên Node-RED để đổi màu trạng thái phòng thành Đỏ.
*   **Tự động cho ăn:** Khi thú cưng tiến gần máng thức ăn, cảm biến IR kích hoạt (kéo mức LOW). ESP32 tự động lọc nhiễu và mở Servo xả cám, phục vụ cho kịch bản demo nhanh chóng.
*   **Giám sát môi trường & Cảnh báo:** DHT11 và MQ liên tục đọc thông số. Nếu cảm biến MQ phát hiện nồng độ khí/mùi hôi vượt ngưỡng giới hạn an toàn, Node-RED sẽ chuyển giao diện phòng sang màu Vàng cảnh báo nhân viên dọn dẹp.
*   **Điều khiển từ xa:** Người dùng có thể chủ động bật/tắt đèn và quạt thông qua bảng điều khiển Node-RED, các lệnh này được gửi qua MQTT xuống ESP32 để xử lý lập tức.

---

## Bảng kết nối các chân ESP32 (Pin Mapping)

| Thiết bị | Chức năng | Chân ESP32 (DevKit V1 30 chân) |
| :--- | :--- | :--- |
| **HC-SR04 (Chuồng 1)** | TRIG | GPIO 26 |
| | ECHO | GPIO 25 |
| **HC-SR04 (Chuồng 2)** | TRIG | GPIO 33 |
| | ECHO | GPIO 32 |
| **IR Sensor (Chuồng 1)** | Tín hiệu OUT | GPIO 34 |
| **IR Sensor (Chuồng 2)** | Tín hiệu OUT | GPIO 35 |
| **Servo SG90 (Chuồng 1)**| Xung PWM | GPIO 13 |
| **Servo SG90 (Chuồng 2)**| X
