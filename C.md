# C. Cấu hình docker compose:

## 1. Tạo cấu trúc thư mục và các file

- Tạo thư mục: ~/myapp: Chạy lệnh: `mkdir ~/myapp`
- Chuyển vào trong thư mục ~/myapp: `cd ~/myapp`
- Tạo thư mục: ./myweb và ./nginx: `mkdir ./myweb` `mkdir ./nginx`

 <img width="754" height="177" alt="image" src="https://github.com/user-attachments/assets/5d27a9a7-10ee-43a6-bb20-6bc6a3ef1cd6" />

## 2. Tạo trang web

- Chạy lệnh `nano ./myweb/index.html` khi cửa sổ nano mở ra thì edit file index.html xong rồi `Ctrl O` để lưu và `Ctrl X` để thoát

 <img width="901" height="383" alt="image" src="https://github.com/user-attachments/assets/7d707c82-deee-4fb6-a1c1-781c2a496599" />

-> Thiết lập cấu trúc thư mục làm việc phân cấp. Thư mục ~/myapp là gốc dự án. Thư mục ./myweb chứa tệp tin tĩnh index.html đại diện cho giao diện người dùng. Việc khởi tạo tệp tin này trước nhằm đảm bảo Nginx có dữ liệu đọc ngay khi container khởi chạy.

## 3. Cấu hình Nginx (Reverse Proxy)

- Chạy lệnh `nano ./nginx/nginx.conf` khi cửa sổ nano mở ra thì edit file nginx.conf xong rồi `Ctrl O` để lưu và `Ctrl X` để thoát

 <img width="1034" height="623" alt="image" src="https://github.com/user-attachments/assets/32b38b30-1815-48af-b44a-758e154d6768" />

-> nginx.conf được thiết lập để Nginx hoạt động ở cổng 80, nhận diện tên miền phụ (server_name). Quy tắc định tuyến được chỉ định rõ: URL gốc (/) truy xuất thư mục ./myweb, URL /api được chuyển tiếp (proxy_pass) đến tiến trình Node-RED nội bộ.

## 4. Cấu hình docker-compose.yml

- Chạy lệnh `nano ~/myapp/docker-compose.yml` khi cửa sổ nano mở ra thì edit file docker-compose.yml xong rồi `Ctrl O` để lưu và `Ctrl X` để thoát

 <img width="1140" height="635" alt="image" src="https://github.com/user-attachments/assets/35b9fd49-c34f-4fe8-97d1-586480e765e2" />

- Chạy lệnh `docker compose up -d` để khởi động hệ thống:

 <img width="1475" height="357" alt="image" src="https://github.com/user-attachments/assets/4624b23e-c05c-47c8-bea2-b04a99fa8a45" />

## 5. Thiết lập bảo mật cho Node-RED

- Node-RED sử dụng cơ chế băm (hash) bằng thuật toán bcrypt để mã hóa mật khẩu, tránh lưu trữ dạng văn bản thuần (plaintext).
Tiến trình Node bên trong container được gọi để sinh mã hash. Sau đó, khối lệnh adminAuth trong settings.js được kích hoạt và gán mã hash này
vào để ép buộc người dùng phải xác thực khi truy cập.

- Chạy lệnh `docker run --rm --entrypoint node nodered/node-red -e "console.log(require('bcryptjs').hashSync('matkhaucuaban', 8));"` 

-> Kết quả cho ra 1 dạng chuỗi: $2b$08...

- Chạy lệnh `sudo nano ~/myapp/nodered/settings.js` khi cửa sổ nano mở ra thì edit file settings.js xong rồi `Ctrl O` để lưu và `Ctrl X` để thoát

 <img width="1098" height="673" alt="image" src="https://github.com/user-attachments/assets/c49f22f3-ee9f-46ad-99cd-57a1caa94e25" />

- Chạy lệnh `docker compose restart nodered` để khởi động lại Node_RED

## 6. Kiểm tra kết quả

- Kiểm tra web: mở trình duyệt và nhập vào địa chỉ ip: 192.168.153.128

 <img width="1062" height="382" alt="image" src="https://github.com/user-attachments/assets/e988ebaa-f5de-483e-8165-db9bdf95ec76" />

- Kiểm tra Node-RED: truy cập địa chỉ ip: 192.168.153.128:1880

 <img width="1915" height="1022" alt="image" src="https://github.com/user-attachments/assets/bdfa3e56-0132-46f1-a83d-5becf6b4f179" />

- Đăng nhập:

<img width="1907" height="988" alt="image" src="https://github.com/user-attachments/assets/25004cb1-d5b7-4d9e-be87-48a0b5ad5513" />
