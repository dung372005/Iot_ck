# Hệ Thống Quản Lý Chăm Sóc Thú Cưng Thông Minh (Smart Pet Care Management System)

Dự án IoT hỗ trợ giám sát môi trường và tự động hóa quy trình chăm sóc thú cưng. Hệ thống sử dụng vi điều khiển trung tâm ESP32, thu thập dữ liệu từ các cảm biến, điều khiển cơ cấu chấp hành và giao tiếp theo thời gian thực (Real-time) với Dashboard Node-RED thông qua giao thức MQTT.

## 1. Tính năng chính (Features)
*   **Giám sát môi trường:** Đo nhiệt độ, độ ẩm (DHT11) và cảnh báo chất lượng không khí/khí gas (MQ-6).
*   **Giám sát ánh sáng:** Cảm biến quang trở (LDR) để tự động hóa hệ thống chiếu sáng.
*   **Theo dõi vật cản & Chuyển động:** Sử dụng cảm biến siêu âm (HC-SR04) và hồng ngoại (IR Sensor) để phát hiện sự hiện diện của thú cưng hoặc định mức thức ăn/nước uống.
*   **Điều khiển tự động/Từ xa:**
    *   Đóng/mở cửa tự động (Servo SG90).
    *   Hệ thống làm mát, thông gió (Quạt 5V).
    *   Hệ thống chiếu sáng (LED) và bơm nước/tải lớn (Relay).
*   **Giao diện giám sát (GUI):** Real-time Dashboard trực quan xây dựng trên Node-RED, hỗ trợ giám sát và điều khiển từ xa.

## 2. Yêu cầu phần cứng & Công nghệ (Tech Stack)
*   **Hardware:** ESP32, Module nguồn DC XL4015, DHT11, MQ-6, LDR, HC-SR04, IR Sensor, Servo SG90, Quạt tản nhiệt 5V, Module Relay.
*   **Firmware:** Viết bằng C/C++ (ưu tiên Non-blocking code với `millis()` hoặc FreeRTOS).
*   **IDE:** VS Code + PlatformIO (hoặc Arduino IDE).
*   **IoT Protocol:** MQTT (PubSubClient).
*   **Dashboard:** Node-RED.

## 3. Block Diagram (Sơ đồ khối)

Sơ đồ khối mô tả kiến trúc phần cứng và luồng dữ liệu của hệ thống:

![Block Diagram](./images/block_diagram.png)  
*(Đảm bảo ảnh sơ đồ khối chuẩn đã được lưu tại đường dẫn `./images/block_diagram.png` trong repo)*

## 4. Pin Mapping (Sơ đồ nối chân)

Bảng phân chân GPIO kết nối giữa ESP32 và các module ngoại vi. 

| Thiết bị / Linh kiện | Chức năng | Chân ESP32 (GPIO) | Loại tín hiệu |
| :--- | :--- | :--- | :--- |
| **Cảm biến DHT11** | Đọc nhiệt độ, độ ẩm môi trường | GPIO 15 | Digital Input |
| **Cảm biến khí MQ-6** | Phát hiện khí gas / ô nhiễm | GPIO 34 | Analog Input |
| **Cảm biến LDR** | Đọc cường độ ánh sáng | GPIO 35 | Analog Input |
| **Cảm biến HC-SR04** | Đo khoảng cách (Mức thức ăn/nước) | TRIG: 5, ECHO: 18 | Digital I/O |
| **Cảm biến hồng ngoại (IR)** | Phát hiện chuyển động | GPIO 13 | Digital Input |
| **Quạt 5V (Chuồng 1)** | Làm mát khu vực 1 | **GPIO 27** | Digital Output |
| **Quạt 5V (Chuồng 2)** | Làm mát khu vực 2 | **GPIO 12** | Digital Output |
| **Servo SG90** | Đóng ngắt cửa tự động | GPIO 14 | PWM Output |
| **Đèn LED** | Chiếu sáng / Cảnh báo | GPIO 2 | Digital Output |
| **Relay** | Kích hoạt bơm nước / Tải lớn | GPIO 26 | Digital Output |

> **Lưu ý phần cứng:** 
> * Các chân GPIO ngoại trừ `GPIO 27` và `GPIO 12` đang được đặt làm ví dụ, cần mapping lại đúng với thiết kế schematic (KiCad) thực tế.
> * Cần đảm bảo tách biệt nguồn (AGND/DGND) hoặc dùng module XL4015 hạ áp cấp dòng ổn định cho Quạt và Servo để tránh nhiễu sụt áp cho ESP32.

## 5. Hướng dẫn triển khai (Deployment)

### 5.1. Nạp Firmware (ESP32)
1. Clone repository này về máy tính.
2. Mở thư mục code bằng **VS Code + PlatformIO**.
3. Mở file `src/main.cpp` (hoặc `main.ino`), cấu hình lại các thông số mạng:
   ```cpp
   const char* ssid = "YOUR_WIFI_SSID";
   const char* password = "YOUR_WIFI_PASSWORD";
   const char* mqtt_server = "YOUR_MQTT_BROKER_IP";
