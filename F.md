# F. Gỡ lỗi

## 1. Giám sát trạng thái và Đọc nhật ký (Logs)

- docker compose ps: Sử dụng để kiểm tra nhanh vòng đời của các dịch vụ.
Nếu một container có trạng thái Restarting hoặc Exited, điều đó báo hiệu hệ thống đang gặp sự cố.

- docker logs <tên_container>: Đây là công cụ quan trọng nhất để xem log của container đó từ đó giúp phát hiện lỗi

- Trong quá trình cấu hình tệp settings.js cho Node-RED ở Phần C,
container liên tục bị sập (Restarting). Nhờ sử dụng lệnh docker logs mynodered,
em đã phát hiện ra lỗi `SyntaxError: Unexpected token` do thiếu dấu phẩy trong cấu trúc JSON.
Sau khi sửa lại dấu phẩy, hệ thống đã hoạt động bình thường trở lại.

## 2. Thiết lập cơ chế Healthcheck và Giới hạn tài nguyên phần cứng

- Một container báo trạng thái Up chỉ có nghĩa là tiến trình đang chạy, không đảm bảo rằng API bên trong thực sự phản hồi
Em đã cấu hình thêm healthcheck cho service myapp (Python Flask)

- Cấu hình sử dụng lệnh curl để gọi liên tục vào cổng 9630. Tuy nhiên, do Image python:3.9-slim mặc định không có curl,
em đã phải chỉnh sửa thêm lệnh RUN apt-get install -y curl trong Dockerfile để Healthcheck hoạt động chính xác

 <img width="991" height="501" alt="image" src="https://github.com/user-attachments/assets/6b970741-179c-4f12-b485-6d7bcac92af8" />

- Container myapi_python hiển thị trạng thái Up (healthy), chứng tỏ API phản hồi thành công lệnh curl

 <img width="1693" height="226" alt="image" src="https://github.com/user-attachments/assets/898eefd3-3cfe-4f5d-8f33-48eeac90522e" />

- Trong một hệ thống Orchestration nhiều dịch vụ, nếu một ứng dụng bị rò rỉ bộ nhớ (Memory Leak),
nó có thể dùng hết RAM của máy chủ host, gây sập toàn bộ các dịch vụ khác (lỗi OOM - Out of Memory)

- Sử dụng thuộc tính deploy.resources để khóa mức RAM tối đa của service myapi ở mức 512MB.
Nếu ứng dụng này vượt quá giới hạn, Docker sẽ tự động ngắt (kill) riêng tiến trình này để bảo vệ các dịch vụ như Nginx và Node-RED.

- Cấu hình trong docker-compose.yml

 <img width="898" height="637" alt="image" src="https://github.com/user-attachments/assets/56341ee0-a85a-4c6e-8a16-c5e2ae39cf27" />

## 3. Giám sát hiệu năng theo thời gian thực

- Sử dụng lệnh `docker compose stats` để tạo ra một bảng điều khiển luồng (stream) hiển thị mức tiêu thụ CPU, RAM, và Băng thông mạng (Network I/O) của từng service

- Dựa vào bảng thống kê, em có thể thấy việc giới hạn 512M là hoàn toàn an toàn và dư dả, vì ứng dụng Flask hiện tại chỉ tiêu thụ một lượng RAM rất nhỏ (khoảng 15-20MB), trong khi Node-RED nặng hơn đáng kể

<img width="1388" height="114" alt="image" src="https://github.com/user-attachments/assets/a6985dba-e1da-4e1a-9110-76b3ad499f2d" />
