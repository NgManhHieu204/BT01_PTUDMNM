# G. Triển khai ứng dụng đến End-user

## 1. Lấy Token trên Cloudflare

- Đăng nhập vào trang quản trị Cloudflare -> Vào mục Zero Trust
- Ở menu bên trái, chọn Networks -> Tunnels -> Bấm nút Create a tunnel
- Chọn loại Cloudflared -> Bấm Next -> Đặt tên cho tunnel -> Save tunnel

<img width="1876" height="927" alt="image" src="https://github.com/user-attachments/assets/6e98b51c-834b-494c-909c-bc1a8a9bf34c" />

- Lấy token: ở màn hình Install and run a connector, chọn tab Docker và copy đoạn Token trong lệnh docker run ... --token ...

`docker run cloudflare/cloudflared:latest tunnel --no-autoupdate run --token eyJhIjoiNTBkY2NkM2U0YThlMDY4NmNlN2VlZGRjZTE1ZDczZTIiLCJ0IjoiZmU2ZmU1ZWQtZDEzZC00NjM2LTk4NGQtMjI1ZDA5NDMwZmM4IiwicyI6Ik0yWTJNMk5qT1dZdE1qRXhaaTAwT0dVeExXSTVaR1F0TkRWaU5HUXpZVEV5TlRnMSJ9`

 <img width="867" height="435" alt="image" src="https://github.com/user-attachments/assets/2162c9e7-7e58-49be-83db-5037afc8d4e8" />

## 2. Chuyển đổi sang docker-compose.yml

- Chạy lệnh `nano ~/myapp/docker-compose.yml` để mở file docker-compose.yml

- Thêm service mytunnel vào:

 <img width="1254" height="604" alt="image" src="https://github.com/user-attachments/assets/9cd95649-205e-4427-b109-3657116a7277" />

- Khởi động tunnel bằng lệnh `docker compose up -d`

<img width="699" height="194" alt="image" src="https://github.com/user-attachments/assets/ad7abb13-2a13-4c92-94c7-9fa7d1279d61" />

- Kiểm tra:

<img width="1469" height="219" alt="image" src="https://github.com/user-attachments/assets/de82f6f0-1a03-49b0-ae46-a49b5882b7b0" />

## 3. Định tuyến Public Hostname

- Quay lại Cloudflare: vào route -> Add route -> Published application. Tại đây em tạo một Public Hostname mới trỏ về hệ thống

  - Tên miền Public: iot.nguyenmanhhieu.id.vn (Sub-domain).

  - Service Backend: `http://mynginx:80`
 
  <img width="975" height="947" alt="image" src="https://github.com/user-attachments/assets/b11d344a-7447-4a33-a3b1-cb0385421ad3" />

-> Do cả mytunnel và mynginx đều nằm chung một Docker Bridge Network (mynetwork), 
hệ thống phân giải tên miền nội bộ (Internal DNS) của Docker cho phép tunnel trỏ traffic 
trực tiếp vào tên container mynginx thay vì dùng địa chỉ IP

<img width="946" height="2047" alt="image" src="https://github.com/user-attachments/assets/594497b6-e8a5-48bf-a232-8d5b7cef9e0c" />

-> Sử dụng điện thoại kết nối mạng 4G (môi trường mạng ngoài), truy cập thành công vào URL public và gọi thành công API dữ liệu Node-RED, hoàn thành chu trình phân phối ứng dụng đến End-User
