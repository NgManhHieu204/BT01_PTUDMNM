# B. Cài đặt Ubuntu + Docker

## 1. Cài đặt hệ điều hành Ubuntu 24.04.4 LTS

- Sử dụng VMware để cài đặt:

b1: Mở VMware, chọn Create a New Virtual Machine
  
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/55eab6f0-7ef6-4a0f-be67-a11b4826b23d" />
    
b2: Chọn Installer disc image file (iso) và trỏ tới file ubuntu-24.04.4-live-server-amd64.iso
  
<img width="966" height="627" alt="image" src="https://github.com/user-attachments/assets/6822f37c-171b-4187-b72f-1c50ebca1833" />

b3: Phân bổ phần cứng: Đặt RAM tối thiểu 2GB (2048MB), CPU 2 cores, ổ cứng 20GB
  
<img width="545" height="547" alt="image" src="https://github.com/user-attachments/assets/e21f1c5b-62df-46e9-b8ab-ff69999619fd" />

b4: Quá trình cài đặt Ubuntu diễn ra cần thiết lập username, password và đánh dấu tích [X] vào mục Install OpenSSH server
  
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/6b9d991c-1a58-4644-a08b-9557ffbd84a8" />

- Cấu hình mạng của Ubuntu để cho phép truy cập SSH vào Ubuntu từ cmd của Windows: Môi trường mạng vật lý (Wi-Fi) có chính sách hạn chế cấp phát
dải IP phụ cho các thiết bị ảo. Do đó, cấu hình Network Adapter trong VMware được thiết lập ở chế độ NAT (Network Address Translation) thay vì Bridged.

b1: Tắt máy ảo (Power off). truy cập Virtual Machine Settings -> Network Adapter -> Chọn NAT: Used to share the host's IP address để máy ảo nhận địa chỉ IP

<img width="928" height="500" alt="image" src="https://github.com/user-attachments/assets/e9c8428c-6b02-4bd2-ae6a-ecc612f17e8e" />

b2: Tại màn hình dòng lệnh của Ubuntu, đăng nhập và thực thi lệnh `ip -4 addr` để kiểm tra địa chỉ IP được cấp phát:

<img width="1100" height="704" alt="image" src="https://github.com/user-attachments/assets/e3e1c030-8e9a-4f23-8b9b-d367a0e74003" />

b3: Mở CMD trên máy tính Windows và thực thi lệnh `ssh hieu@192.168.153.128` để kết nối SSH (sử dụng địa chỉ IP đã lấy được) và xác nhận khóa bảo mật và nhập mật khẩu để hoàn tất quá trình xác thực:
 
<img width="1324" height="927" alt="image" src="https://github.com/user-attachments/assets/4d1455c3-7d69-41ee-b7c7-0ed1a1bb8193" />

## 2. Tìm hiểu các lệnh cơ bản của ubuntu

- Dùng `mkdir TestF` để tạo thư mục mới có tên là TestF
- Dùng `cd TestF` để truy cập vào thư mục TestF
- Dùng `echo "hello world" > filegoc.txt` để tạo 1 file gốc có chứa nội dung bên trong
- Dùng `cp filegoc.txt bancopy.txt` để sao chép file gốc đó thành một bản sao mới
- Dùng `ls -l` để liệt kê danh sách các file
- Dùng `sudo chmod 777 filegoc.txt` để phân quyền cấp độ cao nhất (777 - Đọc/Ghi/Thực thi) cho file gốc
- Dùng `ls -l` để liệt kê lại một lần nữa và xem sự thay đổi (filegoc.txt chuyển sang màu xanh lá cây)

  <img width="722" height="383" alt="image" src="https://github.com/user-attachments/assets/a4574cd3-8231-47aa-a2d6-67df12a2d74b" />

- Dùng `sudo nano filegoc.txt` để thao tác chỉnh sửa file: Khi màn hình chuyển sang giao diện soạn thảo có chữ Nano ở trên cùng, chỉnh sửa nội fung file
theo ý muốn rồi sau đó nhấn Ctrl+O -> Enter để lưu và Ctrl+X để thoát

  <img width="1195" height="710" alt="image" src="https://github.com/user-attachments/assets/6738173c-90b7-4948-a7a1-d31f142d8321" />

## 3. Cài đặt docker cho Ubuntu

- Docker Engine bao gồm ba thành phần chính: Docker Daemon (tiến trình nền quản lý container),
Docker API (giao diện lập trình), và Docker CLI (công cụ dòng lệnh).
Việc sử dụng script tự động từ get.docker.com đảm bảo hệ thống tải về phiên bản ổn định
mới nhất và tự động cấu hình các gói phụ thuộc cần thiết

b1: Cập nhật package index của hệ điều hành: `sudo apt update`

b2: Tải script cài đặt: `curl -fsSL https://get.docker.com -o get-docker.sh`

b3: Thực thi script với quyền quản trị: `sudo sh get-docker.sh`

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/4285fcce-62c9-4980-91eb-07eee2f6a0ff" />

## 4. Kiểm tra phiên bản docker vừa cài đặt, kiểm tra phiên bản của docker compose

- Sử dụng `sudo docker --version` để kiểm tra phiên bản docker và  `docker compose version` để kiểm tra phiên bản docker compose
- Giải thích thêm: Do chưa được phân quyền, lệnh truy vấn Docker Engine yêu cầu quyền quản trị cao nhất (sudo).
Công cụ Docker Compose đã được tích hợp sẵn dưới dạng một plugin nên lệnh là `docker compose` thay vì là `docker-compose`

  <img width="570" height="138" alt="image" src="https://github.com/user-attachments/assets/69ffff5a-4e53-4949-8e54-76c9d430960c" />

## 5. Cấu hình để docker chạy mà không cần tiền tố sudo

- Mặc định, tiến trình Docker Daemon liên kết với một Unix socket thuộc quyền sở hữu của người dùng root. 
Lệnh dưới đây sẽ thêm người dùng hiện tại (biến $USER) vào nhóm phân quyền có tên là docker. 
Sau khi áp dụng, người dùng này được phép tương tác trực tiếp với socket của Docker mà không cần gọi quyền sudo.

  <img width="582" height="153" alt="image" src="https://github.com/user-attachments/assets/85e45314-ef06-4e08-a84e-d54bcee3e1fb" />

- Đăng xuất khỏi phiên SSH hiện tại bằng lệnh `exit`, sau đó tiến hành đăng nhập lại và tiến hành kiểm tra lại quyền bằng lệnh mà không cần sudo

  <img width="486" height="79" alt="Screenshot 2026-04-11 165028" src="https://github.com/user-attachments/assets/6a83a87f-4cec-412a-8234-61c513f37948" />

## 6. Tìm hiểu tập lệnh của docker và docker compose

- Lệnh Docker đơn lẻ quản lý vòng đời của từng Container riêng biệt (khởi tạo, chạy, dừng, xóa)

- Docker Compose là công cụ quản lý kiến trúc đa dịch vụ. Nó đọc cấu hình từ tệp .yml
để đồng bộ hóa khởi chạy nhiều container cùng lúc trên một mạng nội bộ chung.

- Các lệnh thực hành cơ bản:

  - docker ps: Hiển thị danh sách các container đang ở trạng thái hoạt động. Tham số -a hiển thị toàn bộ lịch sử container

  - docker images: Liệt kê các bộ images đã được tải về lưu trữ cục bộ.

  - docker compose up -d: Đọc tệp cấu hình, tạo và khởi chạy các dịch vụ. Tham số -d (detach) ngắt tiến trình khỏi màn hình console để container chạy ngầm.

  - docker logs <tên_container>: Xuất nhật ký hoạt động của container phục vụ quá trình dò lỗi.

## 7. Đảm bảo tường lửa trên Ubuntu đã cho phép các cổng 80, 1880, 9630

- Hệ điều hành Ubuntu sử dụng UFW để kiểm soát lưu lượng mạng.
Các dịch vụ phần mềm đang chạy bên trong Docker cần được mở các cổng (port) tương ứng trên tường lửa để nhận yêu cầu từ mạng bên ngoài.
Cổng 22 (SSH) được ưu tiên mở để duy trì kết nối dòng lệnh.
- Thực thi: `sudo ufw allow 22` `sudo ufw allow 80` `sudo ufw allow 1880` `sudo ufw allow 9630` `sudo ufw enable` `sudo ufw status`

<img width="1052" height="673" alt="image" src="https://github.com/user-attachments/assets/b69c5d65-7ade-4ad7-9384-207b3124eb0f" />
