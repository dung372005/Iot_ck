# HỆ THỐNG QUẢN LÝ CHĂM SÓC THÚ CƯNG THÔNG MINH

## Bài toán

### Nhu cầu thực tế

Việc chăm sóc thú cưng tại các trạm thú y hoặc khách sạn thú cưng hiện nay đòi hỏi sự giám sát liên tục và tiêu tốn nhiều nhân lực. Người quản lý phải theo dõi thủ công tình trạng trống/đầy của từng chuồng, thời điểm cần cho ăn, cũng như chất lượng môi trường không khí (để dọn dẹp kịp thời). Việc quản lý thủ công này dễ dẫn đến thiếu sót, làm giảm chất lượng dịch vụ và ảnh hưởng đến sức khỏe của vật nuôi. Những vấn đề này cho thấy sự cần thiết của một hệ thống quản lý chăm sóc thú cưng thông minh đa chức năng:

*   **Quản lý không gian chuồng tối ưu:** Giúp nhân viên dễ dàng xác định chuồng nào đang trống, chuồng nào có thú cưng để sắp xếp hợp lý.
*   **Tự động hóa chăm sóc:** Tự động phát hiện khi thú cưng tiến đến máng ăn và xả lượng thức ăn phù hợp, giảm bớt công việc thủ công.
*   **Giám sát môi trường & cảnh báo an toàn:** Theo dõi nhiệt độ, độ ẩm tổng thể và phát hiện mùi hôi/chất thải (khí NH3) để đưa ra cảnh báo dọn dẹp kịp thời.

### Nguồn cảm hứng cho dự án

*   **Các hệ thống đỗ xe thông minh:** Dự án lấy cảm hứng từ logic quản lý slot (vị trí) của bãi đỗ xe thông minh, biến tấu thành việc quản lý độc lập từng không gian chuồng thú y, kết hợp với các cơ cấu chấp hành (quạt, đèn) để tạo thành vòng lặp khép kín.
*   **Nhu cầu nâng cao chất lượng dịch vụ thú y:** Phát triển một mô hình đơn giản, chi phí thấp với ESP32 nhưng có thể mở rộng, giúp các cơ sở chăm sóc quy mô nhỏ dễ dàng tiếp cận công nghệ IoT.

---

## Nội dung chính

### 1. Phần cứng

*   **Vi điều khiển:** ESP32 DevKit V1 (30 chân)
*   **Module nguồn:** XL4015 (Hạ áp ổn định 5V cho cụm động cơ, quạt và cảm biến khí)
*   **Khu vực giám sát chuồng:**
    *   2 x Cảm biến siêu âm HC-SR04: Phát hiện sự hiện diện của thú cưng tại 2 chuồng độc lập.
*   **Cổng vào/ra (Cơ cấu xả thức ăn):**
    *   2 x Cảm biến hồng ngoại (IR Sensor): Nhận diện thú cưng khi tiến gần khay ăn.
    *   2 x Động cơ Servo SG90: Mở nắp xả thức ăn tự động khi có tín hiệu từ cảm biến IR.
*   **Hệ thống môi trường và an toàn:**
    *   1 x Cảm biến DHT11: Giám sát nhiệt độ và độ ẩm không khí tổng thể.
    *   1 x Cảm biến MQ: Lắp tại vách ngăn thông gió sát trần để phát hiện mùi hôi/chất lượng không khí chung của 2 chuồng.
    *   4 x Đèn LED (2mm): Bố trí mỗi chuồng 2 đèn, đấu song song chung 1 chân GPIO qua điện trở hạn dòng.
    *   2 x Quạt 5V: Điều khiển thông gió độc lập cho mỗi chuồng.

### 2. Phần mềm

*   **Môi trường phát triển:** PlatformIO (Viết mã C/C++ non-blocking).
*   **Giao thức truyền thông:** MQTT thông qua Broker trực tuyến (ví dụ: EMQX, HiveMQ).
*   **Bảng điều khiển (Node-RED Dashboard):** Hiển thị trạng thái, điều khiển thủ công và cảnh báo môi trường.

### 3. Ý tưởng hoạt động

*   **Giám sát hiện diện:** Các cảm biến siêu âm HC-SR04 liên tục đo khoảng cách. Khi có vật nuôi, dữ liệu được ESP32 cập nhật lên Node-RED để đổi màu trạng thái phòng thành Đỏ.
*   **Tự động cho ăn:** Khi thú cưng tiến gần máng thức ăn, cảm biến IR kích hoạt (kéo mức LOW). ESP32 tự động lọc nhiễu và mở Servo xả cám, phục vụ cho kịch bản demo nhanh chóng.
*   **Giám sát môi trường & Cảnh báo:** DHT11 và MQ liên tục đọc thông số. Nếu cảm biến MQ phát hiện nồng độ khí/mùi hôi vượt ngưỡng giới hạn an toàn, Node-RED sẽ chuyển giao diện phòng sang màu Vàng cảnh báo nhân viên dọn dẹp.
*   **Điều khiển từ xa:** Người dùng có thể chủ động bật/tắt đèn và quạt thông qua bảng điều khiển Node-RED, các lệnh này được gửi qua MQTT xuống ESP32 để xử lý lập tức.

### 4. Sơ đồ khối

<img width="778" height="433" alt="b97639fd-eee5-4712-841d-fcbcd1e58c5d" src="https://github.com/user-attachments/assets/3167cd4e-0d80-45b4-a2c2-e720b03bcc60" />

---

## Bảng kết nối các chân ESP32 (Pin Mapping)

| Thiết bị | Chức năng | Chân ESP32 (DevKit V1 30 chân) |
| :--- | :--- | :--- |
| **HC-SR04 (Chuồng 1)** | TRIG / ECHO | GPIO 26 / GPIO 25 |
| **HC-SR04 (Chuồng 2)** | TRIG / ECHO | GPIO 33 / GPIO 32 |
| **IR Sensor (Chuồng 1)** | Tín hiệu OUT | GPIO 34 |
| **IR Sensor (Chuồng 2)** | Tín hiệu OUT | GPIO 35 |
| **Servo SG90 (Chuồng 1)**| Xung PWM | GPIO 13 |
| **Servo SG90 (Chuồng 2)**| Xung PWM | GPIO 14 |
| **Cảm biến DHT11** | Tín hiệu DATA | GPIO 4 |
| **Cảm biến MQ** | Tín hiệu Analog | GPIO 36 (VP - ADC) |
| **Quạt 5V (Chuồng 1)**| Điều khiển bật/tắt | GPIO 27 |
| **Quạt 5V (Chuồng 2)**| Điều khiển bật/tắt | GPIO 12 |
| **Đèn LED (Chuồng 1)** | Cụm 2 đèn | GPIO 16 |
| **Đèn LED (Chuồng 2)** | Cụm 2 đèn | GPIO 17 |
| **Nguồn XL4015** | 5V & GND | Cấp chung cho Servo, Quạt, MQ |

---

### 5. Giao tiếp MQTT

Hệ thống sử dụng broker công khai (HiveMQ hoặc EMQX). Tất cả các topic dùng tiền tố riêng có dạng `hotel/...` để phân loại dữ liệu.

| Topic | Hướng | Nội dung (Payload) |
| :--- | :--- | :--- |
| `hotel/env` | ESP32 → Node-RED | JSON: nhiệt độ, độ ẩm, chỉ số không khí tổng thể |
| `hotel/cage/<id>/status` | ESP32 → Node-RED | JSON: trạng thái phòng (trống/có vật) và tình trạng đòi ăn |
| `hotel/cage/<id>/cmd` | Node-RED → ESP32 | Lệnh điều khiển dạng JSON: `{ "fan": 1, "light": 0 }` |
| `hotel/status` | ESP32 → Node-RED | `online` / `offline` (Last Will and Testament) |

### 6. Dashboard Node-RED

*   **Trạng thái phòng:** Xây dựng bằng `ui_template` (HTML/CSS), tự động đổi màu theo 3 trạng thái: Xanh (Phòng trống), Đỏ (Có thú cưng), Vàng (Môi trường ô nhiễm, cần dọn dẹp).
*   **Môi trường:** Biểu đồ Line Chart hiển thị nhiệt độ, độ ẩm và chỉ số chất lượng không khí (khí MQ) theo thời gian.
*   **Điều khiển thiết bị:** Cung cấp các công tắc (Switch) để chủ động điều khiển bật/tắt độc lập Quạt thông gió và Đèn chiếu sáng tại từng chuồng.
*   **Nhật ký sự kiện:** Ghi nhận và hiển thị các cảnh báo trạng thái (ví dụ: "Chuồng 1 vừa xả thức ăn", "Chuồng 2 đang cần dọn dẹp").
*   **Trạng thái thiết bị:** Hiển thị tín hiệu kết nối online/offline từ ESP32.

### 7. Hình ảnh linh kiện

| | | |
|:---:|:---:|:---:|
| <img width="220" alt="ESP32" src="https://github.com/user-attachments/assets/9380072d-c243-43f8-b64c-028f3c269812" /><br>**Hình 1.** Vi điều khiển ESP32 | <img width="220" alt="DHT11" src="https://github.com/user-attachments/assets/939949de-78d7-4589-bd78-2a7c100a1174" /><br>**Hình 2.** Cảm biến DHT11 | <img width="220" alt="MQ" src="https://github.com/user-attachments/assets/a94ddc4d-8fea-4be2-b512-97166be84f8a" /><br>**Hình 3.** Cảm biến khí MQ |
| <img width="220" alt="HCSR04" src="https://github.com/user-attachments/assets/9aa1e7dc-926c-48ed-8a57-c3406caf15e9" /><br>**Hình 4.** Cảm biến HC-SR04 | <img width="220" alt="IR" src="https://github.com/user-attachments/assets/94104d26-1c7a-4fca-a5ff-61d7ed4b5145" /><br>**Hình 5.** Cảm biến IR | <img width="220" alt="Servo" src="https://github.com/user-attachments/assets/bab10947-9519-4022-9ce9-dd13458822a5" /><br>**Hình 6.** Servo SG90 |
| <img width="220" alt="Fan" src="https://github.com/user-attachments/assets/093b2657-e376-4b20-9bf6-c75fd6560e7e" /><br>**Hình 7.** Quạt tản nhiệt 5V | <img width="220" alt="LED" src="https://github.com/user-attachments/assets/e35ccaa7-97d6-4e60-91d1-c9ae8573d3a4" /><br>**Hình 8.** Đèn LED & Trở | <img width="220" alt="XL4015" src="https://github.com/user-attachments/assets/31a68011-caf5-4df5-b75e-f462aa844db0" /><br>**Hình 9.** Nguồn XL4015 |

### 8. Tài liệu tham khảo

1. Random Nerd Tutorials, [ESP32 MQTT Publish/Subscribe with Node-RED](https://randomnerdtutorials.com/)
2. Hackaday.io, [Project Aura: Open-Source ESP32 Air Quality](https://hackaday.io/)
3. Wokwi – [ESP32 Simulator](https://wokwi.com/)
4. [Node-RED Documentation](https://nodered.org/docs/)
5. [NewPing Library](https://bitbucket.org/teckel12/arduino-new-ping/wiki/Home) - Thư viện hỗ trợ non-blocking cho cảm biến siêu âm.
