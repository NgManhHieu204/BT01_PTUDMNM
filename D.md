# D. Xây dựng API "Tiên tri vận mệnh Đồ án"

## 1. Khởi tạo mã nguồn và tệp tin môi trường

- Khởi tạo thư mục ./myapi, tạo tệp requirements.txt và app.py

 <img width="572" height="151" alt="image" src="https://github.com/user-attachments/assets/c4f4736e-f5d0-440e-ab07-5de9b245fa04" />

## 2. Viết mã nguồn Python (app.py)

 <img width="1412" height="667" alt="image" src="https://github.com/user-attachments/assets/1e7c8c23-4f17-402e-9d1b-9dc0d88172e4" />

## 3. Tạo Dockerfile

- Khác với Nginx hay Node-RED sử dụng Image có sẵn trên Docker Hub,
ứng dụng Python này cần được tự đóng gói (Build) thành một Image riêng biệt.
Dockerfile được sử dụng như một bản thiết kế: sử dụng lõi python:3.9-slim để tối ưu dung lượng, sao chép mã nguồn vào /app,
tự động chạy lệnh pip install để cài đặt Flask, và khai báo cổng giao tiếp 9630.

 <img width="979" height="492" alt="image" src="https://github.com/user-attachments/assets/5e4b751d-72cf-45a3-b711-0e2f99a14b18" />

## 4. Tích hợp vào hệ thống qua Docker Compose

- Cập nhật tệp docker-compose.yml để bổ sung dịch vụ mới có tên là myapp. Điểm khác biệt quan trọng là thay vì dùng tham số image:, dịch vụ này sử dụng tham số build: ./myapi để yêu cầu Docker Engine tự động đọc Dockerfile và biên dịch image cục bộ ngay tại thời điểm khởi chạy. Dịch vụ này cũng được gắn vào mạng mynetwork chung để giao tiếp với Nginx.

 <img width="891" height="603" alt="image" src="https://github.com/user-attachments/assets/99e980b3-c766-40cd-812b-7d18c283a969" />

## 5. Định tuyến lại nginx (Reverse Proxy)

- Cập nhật quy tắc trên tường lửa Nginx. Khối lệnh location /api/ được điều chỉnh tham số proxy_pass để chuyển tiếp toàn bộ lưu lượng truy cập thay vì vào Node-RED, nay sẽ được điều hướng sang dịch vụ myapp tại cổng 9630. Nginx đóng vai trò là cửa ngõ trung gian tiếp nhận yêu cầu từ người dùng và trả về phản hồi JSON từ ứng dụng Python.

 <img width="1105" height="651" alt="image" src="https://github.com/user-attachments/assets/97a4a615-2eac-4c60-b780-e85c69a871c7" />

## 6. Build và chạy hệ thống

 <img width="689" height="173" alt="image" src="https://github.com/user-attachments/assets/c98e6297-31c0-48e9-9cec-097bb2a8bee0" />

- Truy cập: http://192.168.153.128/api/ để xem kết quả

 <img width="743" height="434" alt="image" src="https://github.com/user-attachments/assets/183adc39-e234-4e6a-b434-47fce286a676" />

- Nâng cấp giao diện

 <img width="1865" height="973" alt="image" src="https://github.com/user-attachments/assets/93956610-3a2a-48fd-8167-3bad8378f1d0" />

