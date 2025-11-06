### web_bt3
# Hình 1: 
Bước 1: Cài và khởi động Docker Desktop
Bước đã làm: Cài Docker Desktop, mở tab Containers, Docker Engine đang chạy.
Ý nghĩa: Hoàn tất nền tảng ảo hóa/container để triển khai toàn bộ bài trên Linux/Docker.
Nhận xét trên ảnh:
Dòng trạng thái “Engine running” ở góc trái dưới.
Phiên bản ứng dụng v4.49.0 ở góc phải dưới.
Chưa có container nào chạy ở thời điểm chụp.
Liên hệ yêu cầu: Đáp ứng Mục (1) cài môi trường và Mục (2) cài Docker (đã sẵn sàng cho các bước kế tiếp).
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/241eae82-f8f2-4efd-ab37-03992cc90610" />

# Hình 2: 
Bước 2: Dựng các dịch vụ bằng docker-compose

Bước đã làm: Khởi động stack các dịch vụ bắt buộc từ docker-compose.
Ý nghĩa: Tạo hạ tầng dữ liệu, giám sát và web server phục vụ cho backend/frontend.
Nhận xét trên ảnh:
Các container đang chạy: mariadb, influxdb, phpmyadmin, grafana, nginx (đều đèn xanh).
Cổng ánh xạ:
MariaDB 3306:3306
InfluxDB 8086:8086
phpMyAdmin 8080→80
Grafana 3000:3000
Nginx 443:443 (có thêm cổng khác ở “Show all ports (2)”).
CPU/RAM thấp, hệ thống ổn định.
Liên hệ yêu cầu: Đáp ứng Mục (3) “docker-compose.yml có đủ container yêu cầu”.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7875d33b-64c0-41c4-83ad-bf3846e87fc8" />  

# Hình 3:
Bước 3: Cấu hình Nginx làm web server và reverse proxy

Bước đã làm: Tạo virtual host, phục vụ trang index và cấu hình location cho các dịch vụ qua đường dẫn con.
Ý nghĩa: Hợp nhất truy cập qua một máy chủ web duy nhất; định danh domain; chuẩn bị chỗ nhúng Grafana/Node‑RED vào SPA.
Nhận xét trên ảnh:
Truy cập http://localhost:8081 hiển thị “Domain OK: huathithanhhien.com”.
Có liên kết kiểm thử reverse proxy: /nodered và /grafana.
Cấu trúc dự án trong VS Code: docker-compose.yml, nginx/conf.d/app.conf, nginx.conf, web/index.html, node-red, .env.
Liên hệ yêu cầu: Đáp ứng Mục (5.1) domain, (5.2) proxy /nodered, (5.3) proxy /grafana. Đồng thời thể hiện hướng SPA một file index.html theo Mục (4).
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e0f6eb5e-9e44-4f7a-8b36-cc5e262e86d9" />

# Hình 4: 
 Bước 4: Truy cập Node‑RED qua đường dẫn con

Bước đã làm: Mở Node‑RED editor qua reverse proxy tại /nodered.
Ý nghĩa: Sẵn sàng xây dựng backend API trả JSON (đối với TMĐT) hoặc các luồng đọc/ghi dữ liệu (đối với IoT).
Nhận xét trên ảnh:
Giao diện editor Node‑RED hiển thị “Flow 1”, thanh công cụ nodes (inject, debug, function…), khu vực Deploy.
URL đang ở http://localhost:8081/nodered — xác nhận reverse proxy hoạt động.
Liên hệ yêu cầu:
Với Mục (4.1) TMĐT: nơi tạo các HTTP endpoint (login, sản phẩm, giỏ hàng, đơn hàng) trả JSON.
Với Mục (4.2) IoT: nơi cấu hình luồng đọc dữ liệu, ghi latest vào MariaDB và lịch sử vào InfluxDB để Grafana hiển thị.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f28c9fac-6b0c-49ea-ad9a-ffb0b21a64f9" />

# Hình 5: 
Mở Grafana qua reverse proxy

Ý nghĩa: Xác nhận dịch vụ Grafana đã hoạt động và truy cập được qua Nginx để phục vụ việc trực quan hóa dữ liệu.
Nhận xét trên ảnh:
URL hiển thị dạng http://localhost:8081/grafana/?orgId=1… cho thấy đang vào Grafana qua đường dẫn con /grafana.
Màn hình “Chào mừng đến với Grafana”, thanh điều hướng bên trái có các mục Trang chủ, Bảng điều khiển, Khám phá, Kết nối…
Trạng thái giao diện tối, ngôn ngữ tiếng Việt, đúng giao diện mặc định sau khi đăng nhập.
Liên hệ yêu cầu: Đáp ứng phần “Nginx reverse proxy đến Grafana” và là công cụ để vẽ biểu đồ thống kê theo yêu cầu đồ án.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1367774d-0ea6-4f0e-8034-0939ba015047" />

# Hình 6: 
Truy cập phpMyAdmin (MariaDB)

Ý nghĩa: Quản trị cơ sở dữ liệu quan hệ, nơi lưu thông tin đăng nhập và các bảng ứng dụng theo đề bài.
Nhận xét trên ảnh:
URL http://localhost:8080 — đang mở trang chính phpMyAdmin.
Khung “Máy chủ cơ sở dữ liệu” hiển thị MariaDB v11.8.3, giao thức TCP/IP.
Collation mặc định đang là utf8mb4_unicode_ci; phần “Người dùng” là iotuser@172.19.0.3.
Bên trái đã có schema iotapp (cùng information_schema).
Khung “Máy chủ Web” cho biết Apache 2.4.65 (Debian), PHP 8.3.27, các extension mysqli, curl, mbstring, sodium.
Liên hệ yêu cầu: Thể hiện thành phần phpMyAdmin và MariaDB đã sẵn sàng để lưu thông tin người dùng/phiên đăng nhập theo đề bài.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6dcc82ea-f776-4177-9918-026e4436b589" /> 

# Hình 7: 
Mở InfluxDB UI (cổng 8086)

Ý nghĩa: Xác nhận cơ sở dữ liệu thời gian thực (time-series) InfluxDB đã hoạt động để lưu lịch sử số liệu, phục vụ hiển thị trên Grafana.
Nhận xét trên ảnh:
URL dạng http://localhost:8086/orgs/…/getting-started — trang “Get Started” của InfluxDB 2.x.
Cột phải có “USEFUL LINKS” (InfluxDB University, Get Started with Flux, Build a Dashboard…).
Góc phải dưới hiển thị phiên bản InfluxDB v2.7.12 cùng thông tin Server/Frontend.
Thanh điều hướng bên trái là bộ icon mặc định của InfluxDB 2.x.
Liên hệ yêu cầu: Đáp ứng phần InfluxDB trong stack, nền tảng để ghi lịch sử dữ liệu IoT.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/81aab2d1-9e66-4582-9963-d90a2414d812" />

# Hình 8: 
Tạo cấu trúc CSDL cho ứng dụng (schema iotapp)

Ý nghĩa: Chuẩn bị các bảng cần thiết cho chức năng đăng nhập/phiên và lưu “giá trị mới nhất” cho dữ liệu giám sát.
Nhận xét trên ảnh:
Đang ở phpMyAdmin, mục Cấu trúc của database iotapp.
Có 3 bảng: latest_metrics, login_sessions, users; Engine InnoDB; Collation utf8mb4_uca1400_ai_ci.
Các thao tác (Duyệt, Cấu trúc, Tìm kiếm, Chèn…) sẵn sàng cho từng bảng; kích thước bảng nhỏ đúng với giai đoạn khởi tạo.
Liên hệ yêu cầu:
users: nơi lưu thông tin người dùng phục vụ đăng nhập.
login_sessions: nơi lưu phiên đăng nhập để duy trì đăng nhập một lần cho đến khi logout.
latest_metrics: nơi lưu giá trị “mới nhất” của các thông số giám sát, phục vụ hiển thị nhanh trên SPA.
 <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a6d7d0b7-1d0d-4d94-8f70-2a365fa67a14" />

 <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d48d4916-8082-40db-b41b-64e7c5d9ee07" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/15bbf618-3e6f-43e6-b4c8-7a03569608bf" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/87fea984-ac11-4c11-8aac-a9024292ce27" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e9b5a0bb-66fa-42b6-b6d7-c4fd9d8af2d5" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ac1aa66b-c3f8-4ea5-b81e-3947486dddef" />  

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d6adcc64-9be8-4445-910f-322cad40bb00" />

