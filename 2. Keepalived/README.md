# Keepalived

- **Keepalived** là một chương trình dịch vụ, một dạng định tuyến "mềm" được viết bằng C, cung cấp các tính năng tạo độ sẵn sàng cao (High Availability) và khả năng cân bằng tải (Load Balancing) cho các hệ thống Linux.

***
Vì sao cần dùng **Keepalived:**

- Trong mô hình triển khai HAProxy:

![5c43e4f3-7eca-4d6d-96dc-16b63f534caf](https://user-images.githubusercontent.com/43572616/177679476-5e29e69f-6809-4f46-aa28-c42070aaadd6.png)

Rất dễ nhận ra điểm yếu của hệ thống nằm ở Loadbalancer HAProxy ! Nếu chẳng may bị sập thì xem như việc tăng số lượng webserver phía sau gần như không còn ý nghĩa gì trong việc tăng khả năng chịu lỗi (fail-over) của dịch vụ

- Vậy giải pháp nâng cấp cho mô hình trên sẽ là xây dựng thêm nhiều Loadbalancer  HAproxy nữa. Khi xây dựng thêm các Loadbalancer cùng chạy song song thì lại nảy sinh ra một vấn đề khác - đó là user sẽ truy cập vào đâu vì cùng một dịch vụ, không thể đưa cho user 2 IP truy cập được

- Để giải quyết bài toán đó, có 1 giải pháp đó là sử dụng Virtual IP ( IP ảo) để user truy cập vào. Các Loadbalacer lúc này sẽ chỉ hoạt động với cùng một V-IP (Virtual IP). Có khá nhiều giải pháp cung cấp tính năng Virtual IP như UCARP,… Trong số đó là Keepalived

***

- **Tính năng:**
  - **Cân bằng tải:** với chức năng health checking (kiểm tra tình trạng sức khoẻ) của các máy chủ trong mô hình HA và các phương thức cân bằng tải xuống server backend

  - **Tạo độ sẵn sàng cao (High Avaiability)** : chức năng VRRP đảm nhận quản lý khả năng chịu lỗi của cụm server (Failover) với Virtual IP.

\* Keepalived mang đến một giải pháp Active-Backup dịch vụ tốt. Nhưng tính năng Load Balancing của Keepalived thì khá yếu, không mạnh mẽ, linh hoạt như Nginx hay HAProxy. Nên thường sử dụng chủ yếu tính năng HA IP Failover của Keepalived, ít dùng đến tính năng cân bằng tải

\* Chương trình keepalived cho phép nhiều máy tính cùng chia sẻ một địa chỉ IP ảo với nhau theo mô hình Active – Passive (có thể cấu hình thêm để chuyển thành mô hình Active – Active nâng cao). Khi người dùng cần truy cập vào dịch vụ, người dùng chỉ cần truy cập vào địa chỉ IP ảo dùng chung này thay vì phải truy cập vào những địa chỉ IP thật của các thiết bị kia.

***
**Keepalived Failover IP hoạt động:**

- Ví dụ có 2 load balancer chạy Nginx (có thể đổi thành HAProxy tùy ý) phân tải cho 2 web server Nginx ở phía sau. 2 load balancer này sẽ được cấu hình dùng chung một VIP. Bình thường thì VIP này sẽ cho node Master phụ trách, node Backup sẽ ở trạng thái chờ:

![keepalived-3 1](https://user-images.githubusercontent.com/43572616/177679691-d26c368a-c0ca-4a60-a581-7076ebe08511.png)

- Khi có sự cố xảy ra với node Master thì node Backup sẽ nhận lấy VIP này và chịu trách nhiệm phân tải về cho các backend server nằm ở sau:

![keepalived-3 2](https://user-images.githubusercontent.com/43572616/177679915-c122a72a-549a-4998-973e-a71282b05ee4.png)

***
**Keepalived trên Linux:**

- Tiến trình dịch vụ của Keepalived khi khởi chạy trên Linux sẽ tạo ra 3 tiến trình cơ bản gồm:

  - Một tiến trình cha có tên gọi là watchdog, sản sinh ra 2 tiến trình con kế tiếp. Tiến trình cha sẽ quản lý theo dõi hoạt động của tiến trình con

  - Hai tiến trình con, một chịu trách nhiệm cho VRRP framework và một chịu trách nhiệm cho health checking (kiểm tra tình trạng sức khoẻ)

|111 Keepalived <-- Parent process monitoring children</p>112 \_ Keepalived <-- VRRP child</p>113 \_ Keepalived <-- Healthchecking child</p>|
| :- |

- Thành phần Linux Kernel mà Keepalived sử dụng:
Keepalived sử dụng 4 module kernel Linux chính sau:

  - **LVS Framework:** dùng để giao tiếp sockets.
  - **Netfilter Framework:** hỗ trợ hoạt động IP Virtual Server (IPVS) NAT và Masquerading.
  - **Netlink Interface:** điều khiển thêm/xoá VRRP Virtual IP trên card mạng.
  - **Multicast:** VRRP advertisement packet được gửi đến lớp địa chỉ mạng VRRP Multicast (224.0.0.18)

***
**Kiến trúc chương trình Keepalived:**

![keepalived-4](https://user-images.githubusercontent.com/43572616/177679827-d97a99f7-2fb3-425f-adb2-fdb0780a02ca.png)

- **WatchDog**: Thư viện framework Watchdog sẽ sản sinh ra các tiến trình con cho hoạt động giám sát tình trạng (VRRP và Healthchecking). Watchdog sẽ giao tiếp với các tiến trình con qua unix domain socket trên Linux để quản lý các tiến trình con.

- **Checkers**: đảm nhận nhiệm vụ kiểm tra tình trạng sức khoẻ của server backup khác trong mô hình mạng Load Balancing.

- **VRRP Stack:** Là tính năng quan trọng nhất của dịch vụ Keepalived. VRRP (Virtual Router Redundancy Protocol)

- VRRP tạo ra một **gateway** dự phòng từ một nhóm các server. Node active được gọI là master server, tất cả các server còn lạI đều trong trạng thái backup. Server master là server có độ ưu tiên cao nhất trong nhóm VRRP.
- Chỉ số nhóm của **VRRP** thay đổI từ 0 đến 255; độ ưu tiên của router thay đổI từ 1 cho đến 254 (254 là cao nhất, mặc định là 100).
- Các gói tin quảng bá của VRRP được gửI mỗI chu kỳ một giây. Các server backup có thể học các chu kỳ quảng bá từ server master.
- Nếu có server nào có độ ưu tiên cao hơn độ ưu tiên của server master thì server đó sẽ chiếm quyền.
- VRRP dùng địa chỉ multicast 224.0.0.18, dùng giao thức IP.

- **SMTP**: Dùng giao thức SMTP để thực hiện gửi email, hỗ trợ công việc quản trị.

- **System Call:** cho phép bạn khởi chạy các script kịch bản hệ thống. Thường dùng cho hoạt động kiểm tra dạng MISC. Đối với VRRP framework, thư viện này cho phép chạy script kịch bản ngoài trong quá trình chuyển đổi trạng thái của giao thức
