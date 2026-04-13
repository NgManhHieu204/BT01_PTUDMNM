# H. Câu hỏi về bài làm?

## 1. Tại sao phải dùng Nginx làm Reverse Proxy mà không trỏ thẳng Tunnel vào Node-RED?

- Tập trung hóa định tuyến (Centralized Routing): Hệ thống được cấu thành từ kiến trúc đa dịch vụ (Multi-service Architecture),
bao gồm Web tĩnh, Node-RED API và Python API. Cloudflare Tunnel chỉ hỗ trợ trỏ đến một điểm cuối (Endpoint) duy nhất.
Nginx đóng vai trò là Application Layer (Layer 7) Reverse Proxy,
có nhiệm vụ tiếp nhận toàn bộ lưu lượng truy cập và thực hiện phân giải đường dẫn (URI Routing) để điều phối gói tin đến đúng dịch vụ backend đích
(ví dụ: định tuyến /api/ tới cổng 9630, /nr/ tới cổng 1880)

- Giải quyết chính sách CORS: Giao tiếp giữa các dịch vụ ở các cổng khác nhau (80, 1880, 9630)
sẽ vi phạm nguyên tắc Same-Origin Policy của trình duyệt web, dẫn đến lỗi Cross-Origin Resource Sharing (CORS).
Nginx đồng nhất hóa mọi yêu cầu truy cập thông qua một cổng 80 duy nhất, xử lý triệt để rào cản bảo mật này tại tầng hạ tầng mạng nội bộ

## 2. Sự khác biệt giữa việc Mount file và Mount thư mục trong Docker là gì?

- Cả hai đều sử dụng cơ chế Bind Mounts, tuy nhiên phạm vi tác động là khác nhau:

  - Mount thư mục (Directory Mount): Thực hiện ánh xạ toàn bộ hệ thống phân cấp (directory tree) từ Host OS vào Container. 
Bất kỳ thay đổi nào (thêm, sửa, xóa tệp) đều được phản ánh đồng bộ hai chiều. 
Cơ chế này phù hợp cho việc chia sẻ mã nguồn hoặc không gian lưu trữ dữ liệu tĩnh (Static Assets)

  - Mount file (File-level Mount): Thực hiện ánh xạ ở cấp độ inode đối với một tệp tin đơn lẻ. 
Điều này cho phép ghi đè cấu hình của một tiến trình cụ thể (như nginx.conf) mà không làm thay đổi, ghi đè, 
hay ẩn đi các tệp tin hệ thống mặc định khác nằm cùng thư mục đích bên trong container

## 3. Nếu thay đổi file index.html ở máy Ubuntu, nội dung trên web có thay đổi ngay không? Tại sao?

- Nếu thay đổi file index.html ở máy Ubuntu, nội dung trên web có thay đổi ngay,
nội dung được cập nhật tức thời mà không cần khởi động lại container

- Giải thích: Bind Mounts trong Docker không thực hiện sao chép dữ liệu vật lý (Data Copy).
Thay vào đó, nó tạo ra một liên kết tham chiếu. Mọi lời gọi hệ thống đọc/ghi (Read/Write System Calls) đối với tệp index.html
bên trong không gian người dùng của container thực chất là các thao tác I/O trực tiếp lên tệp tin vật lý lưu trữ tại Host OS

## 4. docker-compose.yml khai báo các services có phần restart: always hoặc restart: unless-stopped : chúng để làm gì?

- Đây là các chính sách quản lý vòng đời (Restart Policies) nhằm đảm bảo tính sẵn sàng cao (High Availability):

  - always: Docker daemon sẽ liên tục khởi động lại container bất chấp mã trạng thái thoát (Exit Code)
  Kể cả khi tiến trình bị dừng thủ công, hệ thống vẫn tự động đưa container trở lại trạng thái hoạt động khi Docker service khởi động lại.

  - unless-stopped: Tương tự như chính sách always, nhưng có khả năng ghi nhận trạng thái thủ công do quản trị viên thiết lập.
  Nếu container nhận tín hiệu ngắt (SIGKILL/SIGTERM) từ quản trị viên,
Docker sẽ không tự động phục hồi container đó trong các chu kỳ khởi động hệ thống tiếp theo

## 5. Cách khai báo để tất cả các services đều dùng chung 1 network? lợi ích của việc khai báo này là gì? Sửa đổi file docker-compose để tất cả các service đều dùng chung 1 network.

- Cách khai báo: Thiết lập networks ở cấp độ toàn cục (Global Namespace) và ánh xạ tham chiếu mạng đó vào từng định nghĩa của Service:

 ```yaml
services:
  mynginx:
    networks:
      - mynetwork # Tham gia mạng cục bộ
networks:
  mynetwork:
    driver: bridge # Cấu hình Software-Defined Network
```

- Lợi ích: Kích hoạt giao thức phân giải tên miền nội bộ (Internal DNS Resolution) của Docker Engine.
Các container có khả năng thiết lập TCP/IP session với nhau thông qua bí danh định danh (Container Name)
thay vì phụ thuộc vào địa chỉ IP ảo (thường bị cấp phát động và biến đổi). Đồng thời, nó thiết lập ranh giới
cô lập mạng (Network Isolation), ngăn chặn các truy cập mạng không được ủy quyền từ bên ngoài

## 6. Tìm cách đưa Cloudflare Token vào trong file .env rồi sau đó thêm .env vào file .gitignore trước khi push code lên github. Tại sao nói đây là điều quan trọng về bảo mật mã nguồn?

- Quy trình:

  - Khởi tạo tệp .env: echo "TUNNEL_TOKEN=Giá_Trị_Token" > .env

  - Loại trừ theo dõi: echo ".env" >> .gitignore

  - Tham chiếu biến số trong YAML: command: tunnel --no-autoupdate run --token ${TUNNEL_TOKEN}
 
- Phương pháp này tuân thủ nguyên tắc tách bạch Cấu hình và Mã nguồn (Configuration-Code Separation).
".env" duy trì biến môi trường tại máy chủ vật lý, trong khi .gitignore ngăn chặn VCS (Version Control System)
tải tệp tin này lên kho lưu trữ trực tuyến. Nếu Credential (Token) bị rò rỉ dưới dạng văn bản thuần túy (Plaintext) trên Repository,
các đối tượng độc hại có thể mạo danh máy chủ hợp lệ để thiết lập Tunnel trái phép

## 7. Tại sao chúng ta nên thêm hậu tố :ro khi mount file cấu hình Nginx?

- Hậu tố :ro (Read-Only) thực thi nguyên tắc đặc quyền tối thiểu (Principle of Least Privilege).
Khai báo này hạn chế quyền hạn của container tại phân vùng mount, chỉ cho phép thực hiện thao tác Đọc (Read Access).
Trong trường hợp tiến trình Nginx bên trong container bị xâm nhập do lỗ hổng Zero-day,
tác nhân độc hại vẫn không thể thực thi lệnh ghi (Write) để chèn mã độc ngược trở lại tệp cấu hình trên hệ thống tệp của Host OS.

## 8. Khi dùng Cloudflare Tunnel: có cần thiết phải mở cổng cho các service nữa không?

- Khi dùng Cloudflare Tunnel hoàn toàn không cần thiết phải mở cổng cho các service nữa

- Giải thích: Cloudflare Tunnel thiết lập kết nối theo cơ chế hướng ra (Outbound Connection).
Daemon cloudflared sẽ chủ động khởi tạo đường truyền bảo mật (Encrypted Tunnel) tới mạng lưới Edge của Cloudflare.
Do đó, Host OS không yêu cầu mở cổng hướng vào (Inbound Port) tại tầng giao vận (Transport Layer - Layer 4).
Việc loại bỏ các thuộc tính ports: - "80:80" hay ports: - "1880:1880" sẽ triệt tiêu bề mặt tấn công mạng (Attack Surface),
tuân thủ chặt chẽ mô hình kiến trúc bảo mật Zero Trust
