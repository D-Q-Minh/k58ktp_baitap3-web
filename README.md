# k58ktp_baitap3-web
#### Bài tập 3
#### Môn: Phát triển ứng dụng trên nền web

######
Ngày giao   : 2025-10-24 13:50
Hạn nộp     : 2025-11-05 00:00
######
LẬP TRÌNH ỨNG DỤNG WEB trên nền linux
1. Cài đặt môi trường linux: SV chọn 1 trong các phương án
 - enable wsl: cài đặt docker desktop
 - enable wsl: cài đặt ubuntu
 - sử dụng Hyper-V: cài đặt ubuntu
 - sử dụng VMware : cài đặt ubuntu
 - sử dụng Virtual Box: cài đặt ubuntu
2. Cài đặt Docker (nếu dùng docker desktop trên windows thì nó có ngay)
3. Sử dụng 1 file docker-compose.yml để cài đặt các docker container sau: 
   mariadb (3306), phpmyadmin (8080), nodered/node-red (1880), influxdb (8086), grafana/grafana (3000), nginx (80,443)
4. Lập trình web frontend+backend:
 SV chọn 1 trong các web sau:
 4.1 Web thương mại điện tử
 - Tạo web dạng Single Page Application (SPA), chỉ gồm 1 file index.html, toàn bộ giao diện do javascript sinh động.
 - Có tính năng login, lưu phiên đăng nhập vào cookie và session
   Thông tin login lưu trong cơ sở dữ liệu của mariadb, được dev quản trị bằng phpmyadmin, yêu cầu sử dụng mã hoá khi gửi login.
   Chỉ cần login 1 lần, bao giờ logout thì mới phải login lại.
 - Có tính năng liệt kê các sản phẩm bán chạy ra trang chủ
 - Có tính năng liệt kê các nhóm sản phẩm
 - Có tính năng liệt kê sản phẩm theo nhóm
 - Có tính năng tìm kiếm sản phẩm
 - Có tính năng chọn sản phẩm (đưa sản phẩm vào giỏ hàng, thay đổi số lượng sản phẩm trong giỏ, cập nhật tổng tiền)
 - Có tính năng đặt hàng, nhập thông tin giao hàng => được 1 đơn hàng.
 - Có tính năng dành cho admin: Thống kê xem có bao nhiêu đơn hàng, call để xác nhận và cập nhật thông tin đơn hàng. chuyển cho bộ phận đóng gói, gửi bưu điện, cập nhật mã COD, tình trạng giao hàng, huỷ hàng,...
 - Có tính năng dành cho admin: biểu đồ thống kê số lượng mặt hàng bán được trong từng ngày. (sử dụng grafana)
 - backend: sử dụng nodered xử lý request gửi lên từ javascript, phản hồi về json.
 4.2 Web IOT: Giám sát dữ liệu IOT.
 - Tạo web dạng Single Page Application (SPA), chỉ gồm 1 file index.html, toàn bộ giao diện do javascript sinh động.
 - Có tính năng login, lưu phiên đăng nhập vào cookie và session
   Thông tin login lưu trong cơ sở dữ liệu của mariadb, được dev quản trị bằng phpmyadmin, yêu cầu sử dụng mã hoá khi gửi login.
   Chỉ cần login 1 lần, bao giờ logout thì mới phải login lại.
 - hiển thị giá trị mới nhất của các thông số đang giám sát, khi click vào thì hiển thị đồ thị lịch sử quá trình thay đổi (gọi grafana iframe để hiển thị)
 - backend: Sử dụng nodered để đọc dữ liệu từ các cảm biến (có thể dùng api online để lấy dữ liệu theo giời gian thực), 
   nodered sẽ lưu dữ liệu mới nhất (dạng update) vào cơ sở dữ liệu mariadb (sử dụng phpmyadmin để tạp table và quản trị lần đầu)
   nodered sẽ lưu dữ liệu (insert) vào influxdb để lưu giá trị lịch sử, để cho grafana dùng để hiển thị biểu đồ.
5. Nginx làm web-server
 - Cấu hình nginx để chạy được website qua url http://fullname.com  (thay fullname bằng chuỗi ko dấu viết liền tên của bạn)
 - Cấu hình nginx để ht
 - tp://fullname.com/nodered truy cập vào nodered qua cổng 80, (dù nodered đang chạy ở port 1880)
 - Cấu hình nginx để http://fullname.com/grafana truy cập vào grafana qua cổng 80, (dù grafana đang chạy ở port 3000)

#### Bài làm:
##### 
1. Cài đặt môi trường:
Docker Desktop:
<img width="1366" height="591" alt="1" src="https://github.com/user-attachments/assets/bfd81dd6-2094-4891-8f6d-71560ac84dcb" />

wsl:
<img width="345" height="25" alt="2" src="https://github.com/user-attachments/assets/78f9c40a-5c70-438b-bc8a-4010a5cb3781" />
<img width="386" height="56" alt="2 1" src="https://github.com/user-attachments/assets/32623988-6fb4-4ddc-90d5-f4055b5f7cbd" />
<img width="1075" height="584" alt="2 2" src="https://github.com/user-attachments/assets/9d53ee37-2961-4373-b4eb-926883da5505" />
2. Docker có sẵn trên docker desktop

3. cài đặt các docker container:
mariadb (3306), phpmyadmin (8080), nodered/node-red (1880), influxdb (8086), grafana/grafana (3000), nginx (80,443)
sử dụng file docker-compose.yml:
```yaml
#version: "3.8"

services:
  mariadb:
    image: ghcr.io/linuxserver/mariadb:latest
    container_name: mariadb
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Ho_Chi_Minh
      - MYSQL_ROOT_PASSWORD=123456
      - MYSQL_DATABASE=mydb
      - MYSQL_USER=myuser
      - MYSQL_PASSWORD=mypass
    ports:
      - "3306:3306"
    volumes:
      - d:\k58ktp\mariadb\data:/config

  phpmyadmin:
    image: ghcr.io/linuxserver/phpmyadmin:latest
    container_name: phpmyadmin
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Ho_Chi_Minh
      - PMA_ARBITRARY=1
      - PMA_HOST=mariadb
      - PMA_USER=myuser
      - PMA_PASSWORD=mypass
    ports:
      - "8081:80"
    depends_on:
      - mariadb
  nodered:
    image: mirror.gcr.io/nodered/node-red:latest
    container_name: nodered
    restart: always
    ports:
      - "1880:1880"
    environment:
      - TZ=Asia/Ho_Chi_Minh
    volumes:
      - "d:/k58ktp/nodered:/data"
```
<img width="396" height="75" alt="3" src="https://github.com/user-attachments/assets/9c9830e0-d0ef-4740-b9c5-0a36b55ed8d0" />
