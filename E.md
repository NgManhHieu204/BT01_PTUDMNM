# E. Triển khai (level test) ứng dụng

## Kết nối toàn bộ hệ thống (Web → Nginx → Node-RED → API)

## 1. Mở đường (Route) cho Node-RED trên Nginx

- Để tránh lỗi bảo mật CORS của trình duyệt (chặn tải dữ liệu chéo cổng 80 và 1880), ở Nginx mở thêm một cửa phụ có tên là /nr/ để chui thẳng vào Node-RED

<img width="999" height="636" alt="image" src="https://github.com/user-attachments/assets/80f92980-ce5c-4a1e-9de8-4c47aa7e21cd" />

- Khởi động lại nginx: `docker compose restart mynginx`

## 2. Khởi chạy toàn bộ hệ thống

- Chạy lệnh khởi động toàn cục: `cd ~/myapp` `docker compose up -d`

-> Cơ chế Orchestration của Docker Compose giúp toàn bộ kiến trúc (Web Nginx, Node-RED, API Python) tự động khởi chạy, thiết lập mạng nội bộ (mynetwork) và gắn kết các phân vùng dữ liệu (volumes) chỉ với một câu lệnh duy nhất. Hệ thống đạt trạng thái sẵn sàng hoạt động (Production-ready) trong vài giây.

## 3. Giám sát trạng thái Container

- Sử dụng lệnh kiểm tra tiến trình `docker ps -a`

 <img width="1465" height="220" alt="image" src="https://github.com/user-attachments/assets/6832383c-7b68-41b9-98c9-2732b48ca388" />

-> Toàn bộ 3 services (mynginx, mynodered, myapi_python) đều duy trì trạng thái Up, không có hiện tượng Crash Loop (Restarting) -> cấu hình nội bộ đã đạt độ ổn định tuyệt đối

## 4. Kiểm thử độc lập

- Kiểm thử Nginx: truy cập `http://192.168.153.128:80` -> Hiển thị trang chủ index.html

 <img width="1545" height="682" alt="image" src="https://github.com/user-attachments/assets/581c1a61-5a24-4265-b08f-a003b43b4400" />

- Kiểm thử Node-RED: truy cập `http://192.168.153.128:1880` -> Hiển thị màn hình đăng nhập bảo mật của Node-RED, đăng nhập thành công vào luồng làm việc.

 <img width="1863" height="847" alt="image" src="https://github.com/user-attachments/assets/69638a4e-f065-4d82-b7b1-adaa5cdf20f1" />

- Kiểm thử Python API: `http://192.168.153.128:9630`

<img width="814" height="438" alt="image" src="https://github.com/user-attachments/assets/8d1aa5cf-c7ed-410f-8b75-24bf1b5f6640" />

## 5. Khởi tạo REST API bằng Node-RED

- Sử dụng các Node chức năng của Node-RED để thiết kế một API lấy dữ liệu IoT mô phỏng

- Quy trình Flow:
   - http in: Nhận tín hiệu HTTP GET tại endpoint /thong-so
   - function: Xử lý logic và đóng gói dữ liệu JSON (Nhiệt độ, Độ ẩm, Trạng thái)
   - http response: Phản hồi gói tin JSON về cho Client
- Endpoint này sau đó được Nginx định tuyến Reverse Proxy thông qua location /nr/ để ẩn cổng 1880 và tránh rủi ro CORS

 <img width="1863" height="847" alt="image" src="https://github.com/user-attachments/assets/69638a4e-f065-4d82-b7b1-adaa5cdf20f1" />

## 6. Tích hợp API vào giao diện Web Frontend

- Cập nhật tệp ./myweb/index.html, sử dụng Javascript (Fetch API) để thực hiện call AJAX không đồng bộ

- Khi người dùng tương tác với nút bấm, trình duyệt sẽ gửi yêu cầu ngầm tới Nginx. Dữ liệu JSON trả về từ Node-RED (hoặc API Python ở trang Bonus) được bóc tách và chèn trực tiếp (DOM Manipulation) vào giao diện HTML mà không cần tải lại trang.

<img width="1536" height="734" alt="image" src="https://github.com/user-attachments/assets/eed77be0-c284-4f1f-8c7d-a159af2bc2c9" />

- Python API chạy song song

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/7aa8183a-5368-4307-ac7e-320cd4ee5981" />
