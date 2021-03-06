- [Load Balancing](#load-balancing)
- [High Availability](#high-availability)
- [HAProxy](#haproxy)
  - [HAProxy Configuration File](#haproxy-configuration-file)
- [Keepalived](#keepalived)
  - [VRRP protocol](#vrrp-protocol)
  - [Keepalived Configuration File](#keepalived-configuration-file)
***
***
# Load Balancing

- **Load Balancing** (cân bằng tải) là một kỹ thuật bảo đảm máy chủ không bị quá tải bởi lượng truy cập. Với các biện pháp cân bằng tải, khối lượng công việc, truy cập của người dùng được phân chia đều trên các máy chủ. Việc này cung cấp khả năng chịu lỗi cao, đảm bảo tính sẵn sàng phục vụ của hệ thống.

- **Load Balancer** (phần cứng hoặc phần mềm load balancing)**:** là một bộ phận có công việc phân bố đồng đều lưu lượng truy cập giữa hai hay nhiều các máy chủ có cùng chức năng trong cùng một hệ thống. Bằng cách đó, sẽ giúp cho hệ thống giảm thiểu tối đa tình trạng của máy chủ, thậm chí là datacenter
 
- Khi hệ thống có quá nhiều người sử dụng, một server đơn lẻ không đáp ứng được lượng requests khổng lồ được gửi đến cùng lúc. Cần chia sẻ công việc của server hiện tại với các server khác nữa. Khi này, việc tải dữ liệu từ server nào sẽ do bộ cân bằng tải quyết định

![20210826142332791r_7 png](https://user-images.githubusercontent.com/43572616/177664115-48d75793-8d28-4911-8fdf-407511202100.jpg)
***
- **Cách thức hoạt động của Load Balancing:**
  - **Website tiếp nhận lượng truy cập:** website của sẽ nhận một lượng lớn yêu cầu từ client. Các yêu cầu này có thể cùng một lúc đi đến máy chủ thông qua internet.
  - **Phân phối lưu lượng truy cập lên các server:** Sau khi tiếp nhận, load balancer xử lý từng yêu cầu, sau đó điều hướng đến các máy chủ thích hợp.
 
  - **Server tiếp nhận và xử lý yêu cầu:** Sau khi nhận yêu cầu, server có thể tiếp nhận yêu cầu hoặc gửi phản hồi lại với load balancer trong trường hợp không thể tiếp nhận thêm yêu cầu để tránh sự quá tải ở các nút.
 
  - **Server gửi phản hồi cho client**
 ***
- **Session Persistence:**
  - Ví dụ khi mua hàng onl, các sản phẩm trong giỏ hàng của người dùng có thể được lưu trữ ở cấp trình duyệt cho đến khi người dùng sẵn sàng mua chúng

    Việc thay đổi server ở giữa phiên mua sắm có thể gây ra các vấn đề về hiệu suất hoặc gây lỗi giao dịch.

    Như vậy khi đó quan trọng là tất cả các request từ một khách hàng phải được gửi đến cùng một server trong suốt thời gian của phiên. Điều này được gọi là Session Persistence
 
  - Các cân bằng tải tốt nhất có thể xử lý Session Persistence khi cần thiết.
 ***
- **Load Balancer dựa trên phần cứng và phần mềm:**
  - Việc cân bằng tải có thể được thực hiện ở cả phần cứng lẫn phần mềm
    - **Dựa trên phần cứng:** một phần cứng (hoặc phần cứng ảo) một loại thiết bị hoạt động như một Proxy ngược để phân phối lưu lượng mạng hoặc ứng dụng trên các máy chủ khác nhau.
 
    - **Dựa trên phần mềm:** đề cập đến việc sử dụng phần mềm để phân phối và cân bằng lượng người dùng. Được chia thành giao thức 7 Layer và giao thức 4 Layer. Nổi tiếng nhất là Nginx



- So sánh:

| |**Phần cứng**|**Phần mềm**|
| :-: | :-: | :-: |
|**Hiệu năng**|Cao hơn|Thấp hơn|
|**Tính linh hoạt**|Kém linh hoạt|Linh hoạt hơn|
|**Khả năng ảo hóa**|Tích hợp sẵn|Không tích hợp sẵn|
|**Kiến trúc**|Nhiều dung lượng vật lý|Dung lượng vật lý thấp|
|**Giá thành**|Cao, đặc biệt là chi phí bảo trì|Tương đối thấp|
|**Khả năng cấu hình**|Thấp|Cao|
***
- **Khi nào sử dụng LB:**
  - Khi cần sử dụng tính năng cân bằng tải cho service được triển khai trên nhiều server
***
**Phân loại cân bẳng tải:**

  Có nhiều cách cấu hình cân bằng tải, tùy thuộc vào các tính năng đặc thù

 - **Không có cân bằng tải (No Load Balancing):**
  
   Đây là dạng cơ bản nhất cho một ứng dụng web, thường được sử dụng trong môi trường số lượng người dùng ít hoặc không có để test, dev.

   ![khong-can-bang-tai](https://user-images.githubusercontent.com/43572616/177665651-44bbf0bf-f044-4ca3-b5e5-6caf1688231c.png)

   Với mô hình này, người dùng sẽ kết nối trực tiếp với web server tại (yourdoamain.com) mà không sử dụng cân bằng tải. Khi không sử dụng cân bằng tải sẽ dẫn đến một số rủi do: 
   
   - Nếu máy chủ chạy website hoặc chạy phần mềm của công ty cung cấp gặp sự cố, trục trặc thì người dùng sẽ không kết nối được đến ứng dụng web, tất cả công việc ở công ty đều bị đình trệ, để chuyển đổi sang một máy chủ backup thì cũng phải mất rất nhiều thời gian, làm delay nhiều công việc.

   - Khi nhiều kết nối vào một máy chủ làm cho máy chủ đó quá tải nhưng các máy chủ khác/dự phòng lại không chạy gì cả, như thế sẽ không sử dụng hết hiệu năng gây lãng phí tài nguyên và tốc độ truy cập lại bị giảm sút

   - Hệ thống sẽ giảm  tính bảo mật vì người dùng có thể truy cập trực tiếp vào máy chủ

   Vì thế sử dụng cân bằng tải là cần thiết
   
 - **Server Load Balancing:**

   Mục tiêu là phân chia khối lượng công việc ra nhiều máy chủ dựa theo năng lực và tính khả dụng của chúng. Server Load Balancing dựa vào các thông tin ở tầng Application để điều hướng truy cập. Server Load Balancing còn được biết đến như Layer 7 Load Balancing (Cân bằng tải tại tầng 7).

   Đây là cân bằng tải phức tạp nhất nhưng có nhiều tùy biến. Với việc sử dụng cân bằng tải tại tầng 7, có thể điều hướng các request dựa trên thông tin và nội dung của request đó. Cân bằng tải tầng 7 với nhiều backend có thể dùng một domain và port.

   ![photo-1-1531359506259816355323](https://user-images.githubusercontent.com/43572616/177665864-16929487-28a7-4048-b401-14f3b6912c4e.png)


   Ví dụ, có một request gửi tới example.com/something, request đó sẽ được điều hướng đến backend chuyên dụng cho something.

 
 - **Network Load Balancing:**

   Phân chia lưu lượng truy cập giữa các địa chỉ IP, switches, routers sử dụng thiết bị một cách hiệu quả và nâng cao tính ổn định

   Các cấu hình này sẽ được thực hiện ở tầng Transport, do đó, Network Load Balancing còn được gọi với tên Layer 4 Load Balancing (Cân bằng tải tại tầng 4).

   Việc sử dụng cân bằng tải ở tầng 4 (Layer 4 Load Balancing) để có thể cân bằng tải tới nhiều server. Thì các request sẽ được điều hướng dựa trên địa chỉ IP và port. Ví dụ có một request đến example.com/something sẽ được điều hướng tới backend được dùng để điều hướng cho doamin example.com với port 80.

   ![can-bang-tai-layer-4](https://user-images.githubusercontent.com/43572616/177665809-c1bc7a91-c2d2-456e-9788-763d0510154a.png)


  - **Global Server Load Balancing (GSLB):**

    Trong Global Server Load Balancing, một trung tâm điều hành sẽ xử lý việc cân bằng tải giữa khắp nơi trên toàn thế giới thông qua một loạt những thiết bị câng bằng tải Layer 4 và Layer 7

    Trong việc triển khai GSLB, thường sẽ có các thiết bị ADC ở cấp độ toàn cầu lẫn cục bộ, nơi lưu lượng truy cập được phân phối đến.


  - **Container Load Balancing:** 

    Container Load Balancing cung cấp các phiên bản ảo hóa, riêng biệt. Phổ biến nhất hiện nay là hệ thống Kubernetes orchestration, hệ thống này có thể phân chia load giữa các container pods với nhau để giúp nâng cao tính sẵn sàng.


  - **Cloud Load Balancing:**

    Trong hạ tầng Cloud, có tương đối nhiều lựa chọn cho việc cân bằng tải. Cloud Load Balancing bao gồm cả Network Load Balancing (Layer-4) và Application Load Balancing (Layer-7)

***
- **Các thuật toán cân bằng tải:**
  - **Round robin (roundrobin):** 
    - Là thuật toán điều phối vòng tròn, các máy chủ sẽ được xem ngang hàng và sắp xếp theo một vòng quay. Các truy vấn dịch vụ sẽ lần lượt được gửi tới các máy chủ theo thứ tự sắp xếp
 
    - roundrobin là thuật toán được sử dụng mặc định load balancing khi không có thuật toán nào được chỉ định,
    
    Ví dụ khi cấu hình phần backend trong HAProxy - [HAProxy Configuration File](#haproxy-configuration-file), balance sẽ được thêm thuật toán cân bằng tải để kiểm soát cách HAProxy nhận, điều phối request tới các backend server. Nhưng khi không cấu hình cho balance thì mặc định sẽ là thuật toán roundrobin:

        backend web-backend
            option httplog
            option forwardfor
            server web1 192.168.1.110:8080 check
            server web2 192.168.1.111:8080 check
            server web3 192.168.1.112:8080 check
        

    Dựa vào khả năng xử lý của từng server, chúng ta sẽ thay đổi giá trị trọng số của từng server để phân phối tải đến các server khác nhau. Sử dụng tham số “weight” để thay đổi trọng số. Tỷ lệ tải của các server sẽ tỷ lệ thuận trọng số của chúng so với tổng trọng số của tất cả server. Vì vậy mà server nào có trọng số càng cao, thì yêu cầu tải lên nó cũng sẽ cao. Ví dụ cân bằng tải khi thiết lập weight:



        backend web-backend
            balance roundrobin
            option httplog
            option forwardfor
            server web1 192.168.1.110:8080 check weight 2
            server web2 192.168.1.111:8080 check weight 2
            server web3 192.168.1.112:8080 check weight 1


    Khi đó mỗi 05 request, 2 request đầu tiên sẽ được chuyển tiếp lần lượt đến server web1 và web2, 3 request sau sẽ thực hiện chuyển tiếp lần lượt đến server web1, web2 và web3

    Mặc định weight có giá trị là 1, giá trị tối đa của weight là 256. Nếu server giá trị weight là 0, khi đó nó sẽ không tham gia vào cụm server trong load balancing.

---
- **Least connections (leastconn):** 
  - Các requests sẽ được chuyển vào server có ít kết nối nhất trong hệ thống. Thuật toán này được coi như thuật toán động, vì nó phải đếm số kết nối đang hoạt động của server.
 
  - Nó sẽ tự động lựa chọn server với số lượng kết nối đang hoạt động là nhỏ nhất, để lượng connection giữa các server là tương đương nhau.
 
  - Thuật toán này khắc phục được tình trạng một số server có lượng connection rất lớn (do duy trì trạng thái connection), trong khi một số server khác thì lượng tải hay connection thấp

        backend web-backend
            leastconn
            option httplog
            option forwardfor
            server web1 192.168.1.110:8080 check
            server web2 192.168.1.111:8080 check
            server web3 192.168.1.112:8080 check


    Thuật toán này hoạt động tốt khi mà hiệu suất và khả năng tải của các server là tương đương nhau.

---

- **IP Hash (source):** Thuật toán xác định kết nối chính xác từ một IP của máy khách sẽ được kết nối trực tiếp đến một server backend

---
- **Least response time:**  Đây là thuật toán dựa trên tính toán thời gian đáp ứng của mỗi server (response time), thuật toán này sẽ chọn server nào có thời gian đáp ứng nhanh nhất. Thời gian đáp ứng được xác định bởi khoảng thời gian giữa thời điểm gửi một gói tin đến server và thời điểm nhận được gói tin trả lời.

***
- **Các loại thuật toán cân bằng tải (lv\_algo Values for Virtual Server):**

|**Algorithm Name**|**lv\_algo value**|
| :-: | :-: |
|Round-Robin|rr|
|Weighted Round-Robin|wrr|
|Least-Connection|lc|
|Weighted Least-Connection|wlc|
|Locality-Based Least-Connection|lblc|
|Locality-Based Least-Connection Scheduling with Replication|lblcr|
|Destination Hash|dh|
|Source Hash|sh|
|Source Expected Delay|sed|
|Never Queue|nq|

***
***
# High Availability


- **High Availability (HA)** là một giải pháp/quy trình/công nghệ để đảm bảo ứng dụng/cơ sở dữ liệu có thể truy cập được 24/7 trong bất kì điều kiện nào, dù là có dự định trước hay bất ngờ. Nghĩa là là server hoặc thiết bị luôn trong trạng thái sẵn sàng phục vụ, giảm thiểu tình trạng gián đoạn của hệ thống
 
- HA là một lý thuyết chứ không phải là một công nghệ cụ thể, được đo bằng % uptime của dịch vụ đó trong khoảng thời gian, ví dụ HA=99.999%/1 năm. Để nâng cao %HA người ta phải kết hợp nhiều công nghệ đồng thời.
 
- Để ứng dụng và cơ sở dữ liệu có khả năng truy cập 24/7 ở mọi hoàn cảnh thì High availability cần tối thiểu 2 server chạy song song, hoạt động liên tục. Nếu một trong 2 server gặp sự cố, server còn lại sẽ thay thế để đảm bảo cho hệ thống có thể hoạt động bình thường.

***
- **High Availability vs Load Balancing:**
  - Cân bằng tải (Load Balancing): Các node bên hoạt động song song, chia sẻ các tác vụ để năng cao hiệu năng
 
  - Tính sẵn sàng cao (High availability): Các tài nguyên luôn sẵn sàng xử lý yêu cầu, ngay cả khi có vấn đề xảy ra với các thành phần bên trong (hardware, software)

***

**Lợi ích của High availability:**
  - Tất cả dữ liệu trong High availability đều được đảm bảo, dữ liệu trong server được lưu ở các vị trí khác nhau,có thể truy cập hay thao tác khi cần thiết 

![1_lXik7zGdIEYql3GvZ2gkgg](https://user-images.githubusercontent.com/43572616/177666520-40832c47-d0b2-469b-9143-8925adfd39f3.png)

- Nếu VPS (Virtual private server - máy chủ ảo) xảy ra sự cố sập hoặc không hoạt động, dữ liệu sẽ không bị ảnh hưởng
- Người dùng có thể truy cập dễ dàng vào website, truy cập mọi lúc mọi nơi mà không lo bị gián đoạn
- Các bản sao VPS được lưu trữ tại nhiều cụm khác nhau đảm bảo truy cập nếu nếu sự cố xảy ra tại 1 VPS

***
***

# HAProxy

- **HAProxy** là một proxy server, load balancer, system monitoring, được sinh ra để làm nhiệm vụ của một front end web service.

  Là một phần mềm sử dụng trong việc cân bằng tải, chạy trên Linux, Solaris và FreeBSD. Người dùng có thể sử dụng HAProxy để cải thiện suất hoàn thiện của các trang web và ứng dụng bằng cách phân tán khối lượng công việc của chúng trên nhiều máy chủ.
 
- HAProxy cũng được sử dụng trong các hệ thống lớn có lưu lượng truy cập cao như GitHub, Twitter, Reddit, Bitbucket, Stack Overflow,…



![177666641-5dfc3b55-718a-4002-9b14-465ef62204c8](https://user-images.githubusercontent.com/43572616/178094092-b4dbe20d-8a5a-4634-854e-cdc6bb742171.png)

***

- **Khi nào sử dụng HAProxy**

    - **Ví dụ:** Khi  làm việc với dự án nhỏ, web app chỉ cần viết bằng NodeJS dùng Pm2 để có thể chạy multithread sau đó dùng Nginx để serve static file, làm proxy cho nodejs là được. Rất ít khi lỗi, quá tải vì cơ bản là một app nhỏ, nếu sập cũng chỉ cần restart lại
     
      Nhưng khi bước vào một công ty, phải đối mặt với những vấn đề mới, khi lượng request lên tới hàng ngìn request/s thì việc scale hệ thống như thế nào để có thể đáp ứng được lượng request đó là một vấn đề cần phải giải quyết.

      Về cơ bản, khi lượng request nhiều lên, sẽ có thể lựa chọn hai loại hình scale cho hệ thống đó là scale ngang và scale dọc
      - Scale dọc tức là nâng cấp HDD lên SSD dung lượng cao, nâng cấp RAM, Vi xử lí… (đắt, chi phí cao)
 

      - Scale theo chiều ngang tức là dùng thêm nhiều hệ thống tương tự như hệ thống hiện tại để chia nhau xử lí request.

      Thường khi scale sẽ kết hợp cả hai kiểu scale sao cho hợp lí nhất và ít chi phí nhất. Đến đây khi các hệ thống hoạt động cùng với nhau, nảy sinh ra vấn đề làm sao để giao request này cho hệ thống kia xử lí

    => Vì thế Haproxy sinh ra để giải quyết vấn đề này, haproxy sẽ làm vai trò của một proxy server, theo dõi tình trạng các node và sẽ gửi request đến các node

***

- **Tính năng của HAProxy:**
  - Hỗ trợ cân bằng tải ở lớp 4 và lớp 7 (tương ứng với TCP và HTTP).
  - Support HTTP protocol, HTTP / 2, gRPC, FastCGI.
  - **SSL / TLS termination**
    - HAProxy sẽ mã hóa thông tin giao tiếp giữa client và chính nó, sau đó gửi thông tin đã được giải mã tới backend servers. Nghĩa là sẽ làm giảm tải việc mã hóa cho servers.
 
    - **Org:** A TLS termination proxy (or SSL termination proxy, or SSL offloading) is a proxy server that acts as an intermediary point between client and server applications, and is used to terminate and/or establish TLS (or DTLS) tunnels by decrypting and/or encrypting communications.

      The term SSL termination means that you are performing all encryption and decryption at the edge of your network, such as at the load balancer. The load balancer strips away the encryption and passes the messages in the clear to your servers. You might also hear this called SSL offloading

      When you route traffic through an HAProxy load balancer, you gain the ability to terminate SSL at the load balancer. HAProxy encrypts communication between the client and itself and then sends the decrypted messages to your backend servers, which means less CPU work on the servers because there’s no encryption work left to do
  - Lưu trữ chứng chỉ SSL động.
  - **Chuyển đổi nội dung và kiểm tra (Content switching and inspection)**
    - Là chuyển lưu lượng request của client từ một server ban đầu được lựa chọn bởi load balancing sang server khác dựa vào tiêu đề và nội dung của request

      Nghĩa là nó có quyền truy cập vào các requests/responses của ứng dụng. Rồi kiểm tra từng requests/responses sau đó nó đưa ra quyết định về nơi gửi những yêu cầu đó đến và cách xử lý phản hồi.


    - **Org:** Content switching is a programmatic redirection of client request traffic from one server, initially selected by load balancing, to another server that is selected based on the header or content of the request.

      Content-switching is an OSI layer 7 application switching technique, which in lamans terms means that it has access to the HTTP requests and responses of your application. By inspecting each request and response, it make decision on where to send those requests and how to handle responses.

      HAProxy is an open source load balancing tool that also has the ability to implement content switching.

- **Đảm bảo trong suốt (Transparent proxying):**
  - Nếu nhà cung cấp bật logging trong HAProxy thì có thể dễ dàng xem logs truy cập của người dùng/khách hàng (bao gồm cả địa chỉ IP) thông qua câu lệnh: ```docker logs <tên HAProxy Container>```

  - Vì vậy HAProxy có thể có thể được cấu hình để ẩn địa chỉ IP của máy khách khi thiết lập kết nối TCP với máy chủ và máy chủ nghĩ rằng đang thực hiện kết nối trực tiếp đến từ máy khách. Bằng cách thêm dòng ```http-request set-var(txn.src_masked) src,ipmask(number)``` trong frontend tại HAProxy Config file
 
- Ghi nhật ký chi tiết.
- Tương tác servers bằng dòng lệnh (CLI for server management)
- Xác thực HTTP.
- **Đa luồng:** Nhiều người dùng cùng lúc, xử lý nhiều yêu cầu cùng lúc
- **Rewrite URL:** ghi lại địa chỉ website từ dạng này thành một dạng khác. 
- Kiểm tra sức khỏe nâng cao.
- Giới hạn tần số kết nối.

***

- **Bảo mật:**
  - HAProxy được coi là an toàn, nó có rất ít lỗ hổng trong các năm qua
 
  - Nó chứa các tính năng có thể hạn chế tấn công như cô lập chính nó sử dụng chroot, drop ngay user/group không có các quyền đặc biệt khi khởi động và tránh truy cập vào ổ cứng khi khởi động.
 
  - HAProxy cũng có thể được sử dụng để bảo mật cho các hệ thống khác, ví dụ như: HAProxy theo dõi lưu lượng truy cập và giám sát hành vi của khách hàng thông qua các yêu cầu, sau đó có thể chặn khách hàng đó nếu thấy khả nghi. Người dùng có thể cấu hình ACL, xác định các chính sách để kiểm tra dữ liệu của các truy cập. Nó cũng có thể giới hạn tốc độ và danh sách IP Blacklist/Whitelist

***

- **Sticky session:**
  - Trong môi trường web, nhiều khi chúng ta cần cố định session của user, như để duy trì trạng thái login. Khi đó, chúng ta cần cố định session trên một server
 
  - HAProxy hỗ trợ một số thuật toán Load Balancing duy trì trạng thái kết nối mà cho phép cố định session như hdr, rdp-cookie, source, uri hoặc url\_param. Ví dụ:

```
backend cms
    balance source
    hash-type consistent
    server web1 192.168.10.110:8080 check
    server web2 192.168.10.111:8080 check
    server web3 192.168.10.112:8080 check
```


- Nếu muốn cố định session mà vẫn sử dụng các thuật toán load balancing như roundrobin, leastconn, hoặc static-rr, khi đó sử dụng “Sticky Session”. Sticky session cho phép cố định session của users mà sử dụng cookie, và HAProxy sẽ điều phối để luôn request từ một user đến cùng một server.
 
- HAProxy có thể lưu cookie trong trình duyệt của người dùng để ghi nhớ máy chủ nào sẽ gửi lại cho họ. Cookie sẽ chứa unique ID của server và được đảm bảo chỉ thuộc về một người dùng duy nhất, sẽ cung cấp thông tin chính xác để thực hiện cố định phiên (sticky sessions).
 
- Để sử dụng sticky session trong HAProxy, chúng ta thêm tùy chọn “cookie cookie\_name insert/prefix” vào trong phần backend. Chỉnh sửa tệp cấu hình HAProxy: /etc/haproxy/haproxy.cfg . Để tạo cookie trong trình duyệt của người dùng, hãy thêm "cookie" vào phần phụ trợ chứa máy chủ web và thêm thông số cookie vào mỗi dòng máy chủ để đặt giá trị của cookie
 
  - Khi đó sử dụng ‘cookie cookie\_name insert <options>’. “cookie\_name” là giá trị mà HAProxy sẽ chèn vào (insert). Khi client quay lại (tức là cũng là client này và request tiếp theo), HAProxy sẽ biết được server nào để chọn cho client này. Ví dụ:

```
cookie  WEB insert
server web1 192.168.1.110:8080 cookie web1 check
server web2 192.168.1.111:8080 cookie web2 check
server web3 192.168.1.112:8080 cookie web3 check
```


- Khi sử dụng insert, HAProxy phản hồi cho client là “WEB=web1”

***

- **Hạn chế:** Với sticky session, việc các request từ một user sẽ chỉ cố định vào một server. Vì vậy mà sẽ không đảm bảo được tính điều phối nhiều request từ một users đến nhiều server. Để khắc phục điểm hạn chế này, thì hiện nay có một số phần mềm như redis, memcached, … cho phép lưu session của user, còn việc điều phối các request của user thì vẫn thực hiện bình thường đến các server.

***

- **Health Check:**
  - HAProxy sử dụng health check để phát hiện các backend server sẵn sàng xử lý request. Kỹ thuật này sẽ tránh việc loại bỏ server khỏi backend thủ công khi backend server không sẵn sàng. health check sẽ cố gắng thiết lập kết nối TCP tới server để kiểm tra backend server có sẵn sàng xử lý request.
 
  - Nếu health check không thể kết nối tới server, nó sẽ tự động loại bỏ server khởi backend, các traffic tới sẽ không được forward tới server cho đến khi nó có thể thực hiện được health check. Nếu tất cả server thuộc backend đều xảy vấn đề, dịch vụ sẽ trở trên không khả dụ (trả lại status code 500) cho đến khi 1 server thuộc backend từ trạng thái không khả dụ chuyển sang trạng thái sẵn sàng.

***

- **HAProxy Runtime API:** được tích hợp vào HAProxy, cho phép thay đổi cấu hình HAProxy ngày lập tức, đồng thời HAProxy API cũng cho phép chiết xuất dữ liệu thông kê về HAProxy. Không giống như các API HTTP thông thường, HAProxy API chạy thông qua TCP và Unix sockets. Đó là lý do khi cấu hình HAProxy API, ta phải thiết lập socket mới cho HAProxy API (stats socket hoặc socket) và đôi khi trong một số tài liệu nói về HAProxy API lại sử dụng thuật ngữ ‘socket’ để thay thế.

***
***
## HAProxy Configuration File

Cấu hình của HAProxy thường được tạo từ 4 thành phần bao gồm global, defaults, frontend, backend. 4 thành phần sẽ định nghĩa cách HAProxy nhận, xử lý các request, điều phối các request tới các Backend phía sau



- Đường dẫn file cấu hình: /etc/haproxy/haproxy.cfg



- Cấu trúc tổng quan:

|<p>**global**<br># Các thiết lập tổng quan</p><p>**defaults**<br># Các thiết lập mặc định</p><p>**frontend**<br># Thiết lập điều phối các request</p><p>**backend**<br> # Định nghĩa các server xử lý request</p>|
| :- |


- Khi khởi tạo dịch vụ haproxy, các thiết lập tại **global** sẽ được sử dụng để định nghĩa cách HAProxy được khởi tạo như số lượng kết nối tối đa, đường dẫn ghi file log, số process v.v.
 
- Sau đó các thiết lập tại mục **defaults** sẽ được áp dụng cho tất cả mục **frontend**, **backend** nằm phía sau (có thể định nghĩa lại các giá trị mặc định tại **frontend** và **backend**)
 
- Có thể có nhiều mục **frontend**, **backend** được định nghĩa trong file cấu hình.
 
- Mục **frontend** được định nghĩa để điều hướng các request nhận được tới các **backend**. Mục **backend** sử dụng để định nghĩa các danh sách máy chủ dịch vụ (có Web server, Database, …) đây là nơi request được xử lý

***

**Global**

- Mục này luôn đứng riêng 1 dòng và được định nghĩa 1 lần duy nhất trong file cấu hình
 
- Các thiết lập bên dưới global định nghĩa các thiết lập bảo mật, các điều chỉnh về hiệu năng áp dụng trên toàn HAProxy (áp dụng tại mức tiến trình HAProxy hoạt động)
 
- Ví dụ:

```
global
    log         127.0.0.1 local2
    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     4000
    user        haproxy
    group       haproxy
    daemon
    stats socket /var/lib/haproxy/stats
```


- **log**: các cảnh báo phát sinh tại HAProxy trong quá trình khởi động, vận hành sẽ được gửi tới syslog
 
- **maxconn**: Chỉ định giới hạn số kết nối mà HAProxy có thể thiết lập. Sử dụng với mục đích bảo vệ load balancer khởi vấn đề tràn ram
 
- **user / group:** chỉ định quyền sử dụng để khởi tạo tiến trình HAProxy. Linux yêu cầu xử lý bằng quyền root cho những port nhỏ hơn 1024. Nếu không định nghĩa user và group, HAProxy sẽ tự động sử dụng quyền root khi thực thi tiến trình.
 
- **stats socket:** Cho phép kết nối tới socket HAProxy API. Định nghĩa runtime api, có thể sử dụng để disable server hoặc health checks, thay đổi load balancing weights của server

***

**Defaults**

- Khi cấu hình tăng dần, phức tạp, khó đọc, các thiết lập cấu hình tại mục defaults giúp giảm các trùng lặp. Thiết lập tại mục defaults sẽ áp dụng cho tất cả mục frontend backend nằm sau nó. 
 
- Có thể thiết lập lại trong từng mục backend, frontend. Và cũng có thể có nhiều mục defaults. Chúng sẽ ghi đè lên nhau dựa theo vị trí (tức các mục defaults nằm sau sẽ ghi đè lên các mục defaults nằm trước
 
- Ví dụ đơn giản, thế thiết lập mode http tại mục defaults, khi đó toàn bộ các mục frontend, backend, listen sẽ đều dùng mode http làm mặc định.
 

```
mode                    http
log                     global
option                  httplog
option                  dontlognull
option                  http-server-close
option forwardfor       except 127.0.0.0/8
option                  redispatch
retries                 3
option  http-server-close
timeout http-request    10s
timeout queue           1m
timeout connect         10s
timeout client          1m
timeout server          1m
timeout http-keep-alive 10s
timeout check           10s
maxconn                 3000
```

- **timeout connect** chỉ định thời gian HAProxy đợi thiết lập kết nối TCP tới backend server. Hậu tố s tại 10s thể hiện khoảng thời gian 10 giây, nếu bạn không có hậu tố s, khoảng thời gian sẽ tính bằng milisecond
 
- **timeout server** chỉ định thời gian chờ kết nối tới backend server.
 
- Khi thiết lập **mode tcp** thời gian **timeout server** phải bằng **timeout client**
 
- **log global:** Chỉ định ‘**frontend**’ sẽ sử dụng log settings mặc định (trong mục global)
 
- **mode**: Thiết lập mode định nghĩa HAProxy sẽ sử dụng TCP proxy hay HTTP proxy. Cấu hình sẽ áp dụng với toàn **frontend** và **backend** khi chỉ mong muốn sử dụng 1 mode mặc định trên toàn **backend** (Có thể thiết lập lại giá trị tại backend)
 
- **maxconn**: Thiết lập chỉ định số kết nối tối đa, mặc định bằng 2000.
 
- **option httplog:** Bổ sung format log dành riêng cho các request http bao gồm (connection timers, session status, connections numbers, header v.v). Nếu sử dụng cấu hình mặc định các tham số sẽ chỉ bao gồm địa chỉ nguồn và địa chỉ đích
 
- **option http-server-close**: Khi sử dụng kết nối dạng keep-alive, tùy chọn cho phép sử dụng lại các đường ống kết nối tới máy chủ (có thể kết nối đã đóng) nhưng đường ống kết nối vẫn còn tồn tại, thiết lập sẽ giảm độ trễ khi mở lại kết nối từ phía client tới server.
 
- **option dontlognull:** Bỏ qua các log format không chứa dữ liệu
 
- **option forwardfor:** Sử dụng khi mong muốn backend server nhận được IP thực của người dùng kết nối tới. Mặc định backend server sẽ chỉ nhận được IP của HAProxy khi nhận được request. Header của request sẽ bổ sung thêm trường X-Forwarded-For khi sử dụng tùy chọn
 
- **option redispatch:** Trong mode HTTP, khi sử dụng kỹ thuật stick session, client sẽ luôn kết nối tới 1 backend server duy nhất, tuy nhiên khi backend server xảy ra sự cố, có thể client không thể kết nối tới backend server khác (Trong bài toán load balancer). Sử dụng kỹ thuật cho phép HAProxy phá vỡ kết nối giữa client với backend server đã xảy ra sự cố. Đồng thời, client có thể khôi phục lại kết nối tới backend server ban đầu khi dịch vụ tại backend server đó trở lại hoạt động bình thường.
 
- **retries:** Số lần thử kết nối lại backend server trước khi HAProxy đánh giá backend server xảy ra sự cố.
- **timeout check:** Kiểm tra thời gian đóng kết nối (chỉ khi kết nối đã được thiết lập)
- **timeout http-request:** Thời gian chờ trước khi đóng kết nối HTTP
- **timeout queue:** Khi số lượng kết nối giữa client và haproxy đạt tối đã (maxconn), các kết nối tiếp sẽ đưa vào hàng đợi. Tùy chọn sẽ làm sạch hàng chờ kết nối.

***

**Frontend**

- Mục **frontend** định nghĩa địa chỉ IP và port mà client có thể kết nối tới. Có thể có nhiều mục frontend tùy ý, chỉ cần đặt label của chúng khác nhau (frontend <tên>)
 
- Ví dụ:

```
frontend www.mysite.com
    bind 10.0.0.3:80
    bind 10.0.0.3:443 ssl crt /etc/ssl/certs/mysite.pem
    http-request redirect scheme https unless { ssl_fc }
    use_backend api_servers if { path_beg /api/ }
    default_backend web_servers
```

- **bind:** IP và Port HAProxy sẽ lắng nghe để mở kết nối. IP có thể bind tất cả địa chỉ sẵn có hoặc chỉ 1 địa chỉ duy nhất, port có thể là một port hoặc nhiều port (1 khoảng hoặc 1 list).
- **http-request redirect:** Phản hỏi tới client với đường dẫn khác. Ứng dụng khi client sử dụng http và phản hồi từ HAProxy là https, điều hướng người dùng sang giao thức https
- **use\_backend**: Chỉ định backend sẽ xử lý request nếu thỏa mãn điều kiện (Khi sử dụng ACL)
- **default\_backend:** Backend mặc định sẽ xử lý request (Nếu request không thỏa mẵn bất kỳ điều hướng nào)

***

**Backend**

- Mục backend định nghĩa tập server sẽ được cân bằng tải khi có các kết nối tới (VD tập các server chạy dịch vụ web giống nhau).
 
- Ví dụ:

```
backend web_servers
    balance roundrobin
    cookie SERVERUSED insert indirect nocache
    option httpchk HEAD /
    default-server check maxconn 20
    server server1 10.10.10.86:80 cookie server1
    server server2 10.10.10.87:80 cookie server2
```

- **balance**: Kiểm soát cách HAProxy nhận, điều phối request tới các backend server. Đây chính là các thuật toán cân bằng tải.
- **cookie:** Sử dụng cookie-based. Cấu hình sẽ khiến HAProxy gửi cookie tên SERVERUSED tới client, liên kết backend server với client. Từ đó các request xuất phát từ client sẽ tiến tục nói chuyện với server chỉ định. Cần bổ sung thêm tùy chọn cookie trên server line
- **option httpchk:** Với tùy chọn, HAProxy sẽ sử dụng health check dạng HTTP (Layer 7) thay vì kiếm trả kết nối dạng TCP (Layer 4). Và khi server không phản hồi request http, HAProxy sẽ thực hiện TCP check tới IP Port. Health check sẽ tự động loại bỏ các backend server lỗi, khi không có backend server sẵn sàng xử lý request, HAProxy sẽ trả lại phản hồi 500 Server Error. Mặc đinh HTTP check sẽ kiểm tra root path / (Có thể thay đổi). Và nếu phản hồi health check là 2xx, 3xx sẽ được coi là thành công.
- **default-server:** Bổ sung tùy chọn cho bất kỳ backend server thuộc backend section (VD: health checks, max connections, v.v). Theo ví dụ, tùy chọn maxconn 20 sẽ được bổ sung vào tất cả backend server, tức mỗi server sẽ chỉ phục vụ 20 kết nối đồng thời
- **server:** Tùy chọn quan trọng nhất trong backend section. Tùy chọn đi kèm bao gồm tên, IP:Port. Có thể dùng domain thay cho IP.

***

**Listen**

- **listen** là sự kết hợp của cả 2 mục frontend và backend. Vì listen kết hợp cả 2 tính năng backend frontend, nên có thể sử dụng listen thay thế các các mục backend và frontend.
 
- Ví dụ:

```
listen web-backend
    bind 10.10.10.89:80
    balance leastconn
    cookie SERVERID insert indirect nocache
    mode  http
    option  forwardfor
    option  httpchk GET / HTTP/1.0
    option  httpclose
    option  httplog
    timeout  client 3h
    timeout  server 3h
    server node1 10.10.10.86:80 weight 1 check cookie s1
    server node2 10.10.10.87:80 weight 1 check cookie s2
    server node3 10.10.10.88:80 weight 1 check cookie s3
```


- Cú pháp thường dùng:

```
listen web-backend:
    ...
    server node1 10.10.10.86:80 inter <time> rise <number> fall <number>
```


- **inter:** khoảng thời gian giữa hai lần check liên tiếp.
- **rise:** Số lần kiểm tra backend server thành công trước khi HAProxy đánh giá nó đang hoạt động bình thường và bắt đầu điều hướng request tới
- **fall:** Số lần kiểm tra backend server bị tính là thất bại trước khi HAProxy đánh giá nó xảy ra sự cố và không điều hướng request tới.



- **Một cấu hình cơ bản:**

```
global
  log stdout format raw local0 info
  user haproxy 
  group haproxy
  stats socket /var/run/api.sock mode 660 level admin expose-fd listeners
     # Phần cấu hình HAProxy API: 
     # stats socket /var/run/haproxy.sock mode 600 expose-fd listeners level user
    # stats socket ipv4@127.0.0.1:9999 level admin
    # stats timeout 2m
    # server-state-file /var/lib/haproxy/server-state

defaults
  mode http
  timeout client 10s
  timeout connect 5s
  timeout server 10s
  timeout http-request 10s
  log global
  # Phần cấu hình HAProxy API:
     # load-server-state-from-file global

frontend stats
  bind *:8404
  stats enable
  stats uri /
  stats refresh 10s

frontend myfrontend
  bind :80
  default_backend webservers

backend webservers
  server s1 web1:8000 check
  server s2 web2:8001 check
```

- Trong phần Global, stats socket kích hoạt HAProxy Runtime API và seamless reloads của HAProxy
 
- /var/run/api.sock: Đường dẫn tới file socket kết nối tới
 
- ipv4@127.0.0.1:9999: Chỉ định địa chỉ và port được phép kết nối tới HAProxy API
 
- **server-state-file** và **load-server-state-from-file**: Lưu lại trạng thái server. 
  Ví dụ, khi chuyển trạng thái server thành maintain nhưng đây chỉ là trạng thái tạm thời. Đến khi khởi động lại HAProxy, trạng thái server đó sẽ trở lại trạng thái active. Để tránh tình trạng đó xảy ra, sử dụng 2 cấu hình **server-state-file** và **load-server-state-from-file**
 
- frontend đầu tiên sử dụng cổng 8404 để vào bảng điều khiển HAProxy Stats, hiển thị thống kê trực tiếp về bộ cân bằng tải.
 
- frontend thứ hai sử dụng cổng 80 để gửi yêu cầu đến một trong hai webservers được liệt kê trong phần backend webservers





- **Một trong những cấu hình mặc định thường dùng cho HAProxy:**

```
global
    log         127.0.0.1 local2
    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     4000
    user        haproxy
    group       haproxy
    daemon
    stats socket /var/lib/haproxy/stats

defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 3
    timeout http-request    10s
    timeout queue           1m
    timeout connect         10s
    timeout client          1m
    timeout server          1m
    timeout http-keep-alive 10s
    timeout check           10s
    maxconn                 3000

listen stats
    bind :8080
    mode http
    stats enable
    stats uri /stats
    stats realm HAProxy\ Statistics

listen webcluster
    bind :80
    balance  roundrobin
    mode  http
    option  forwardfor
    server web1 10.10.11.87:80 check
    server web2 10.10.11.88:80 check' > /etc/haproxy/haproxy.cfg
```


- Khi chỉnh sửa file cấu hình haproxy.cfg, có thể reload lại load balancer mà không làm gián đoạn lưu lượng truy cập:

`docker kill -s HUP haproxy`

***
***
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



|<p>111 Keepalived <-- Parent process monitoring children</p><p>112 \\_ Keepalived <-- VRRP child</p><p>113 \\_ Keepalived <-- Healthchecking child</p>|
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

***
***
## VRRP protocol

- **VRRP (Virtual Router Redundancy Protocol)** là giao thức được mô tả trong RFC3768,  cho phép sử dụng chung 1 địa chỉ IP gateway cho một nhóm router. Nếu router chính bị sập, ngay lập tức các con router khác sẽ biết, với các nguyên tắc bầu chọn do VRRP quy định, các con còn lại sẽ chọn ra 1 con khác lên thay thế router chính, nắm giữ địa chỉ IP gateway đã được cấu hình từ trước, và lưu lượng từ người dùng sẽ đi qua con gateway mới này => Đảm bảo dịch vụ của người sử dụng thông suốt, không bị gián đoạn.
 
***
- **Tại sao dùng VRRP**
  - Thông thường, từ người dùng (customer) đến nhà cung cấp dịch vụ (ISP) có 1 đường link duy nhất. Và người quản trị sẽ cấp cho customer một địa chỉ IP gateway để đi lên mạng của ISP.

![20070725_121050_image002_207834_1285_0](https://user-images.githubusercontent.com/43572616/179469006-c81ba817-33be-4454-8525-73093d35ef4d.png)

- Có thể thấy, từ phía khách hàng nhìn lên ISP, họ chỉ có một con đường duy nhất để đến ISP. Nhưng trong nhiều trường hợp, có thể router ở biên của nhà mạng bị sập… Điều này dẫn đến gián đoạn dịch vụ của người sử dụng. Để hạn chế trường hợp này, ISP có thể sử dụng 2 hoặc nhiều router để back-up cho nhau (redundancy). Nhưng với 2 hay nhiều router mà chỉ có 1 địa chỉ gateway duy nhất thì cần phải biết cấu hình vào con nào, từ đó VRRP ra đời!

***

- **Hoạt động:**
  - Các VRRP Router trong cùng một VRRP Group tiến hành bầu chọn Master sử dụng giá trị priority đã cấu hình cho router đó. Priority có giá trị từ 0 đến 255. Nguyên tắc cơ bản: Priority cao nhất thì nó là Master, nếu priority bằng nhau thì IP cao hơn là Master.
 
    - Nếu VRRP router được cấu hình virtual ip = primary ip, nó sẽ có priority = 255 và trở thành master luôn. Nếu VRRP router được cấu hình priority = 255 thì mặc nhiên nó sẽ sử dụng primary ip làm virtual ip.
 
 
    - Sau khi lên làm master, các router bắt đầu nhận bản tin advertisement từ router khác thuộc cùng VRRP group cũng như gửi cho các router khác bản tin advertisement chưa các tham số VRRP của nó. Master router sẽ so sánh priority của mình với priority nhận được từ bản tin advertisement, nếu cao hơn và router đang được cấu hình preempt mode = true, nó sẽ giữ nguyên trạng thái master. Nếu không đạt đủ các điều kiện trên, nó sẽ nhảy xuống làm backup.
 
 
    - Trong trường hợp priority bằng nhau, nó sẽ so sánh địa chỉ ip của interface được cấu hình VRRP với src-ip của gói tin advertisement, nếu cao hơn thì giữ nguyên trạng thái master, thấp hơn thì xuống làm backup.
 
 
  - Sau khi bầu chọn xong Master, router nào là Master Router sẽ gửi trả lời bản tin ARP Request cho host sử dụng Virtual MAC
 
 
  - Master Router định kỳ gửi các bản tin Advertisement cho tất cả Virtual Router Backup để thông báo về trạng thái hoạt động của mình. Backup dựa vào các bộ timer của mình để xác định Master có gặp sự cố hay không. Nếu có sự cố, các VRRP Router còn lại trong VRRP Group sẽ tiến hành bầu chọn lại VRRP Master Router theo thứ tự như trên
    - Theo quy định của chuẩn, mặc định các master router sẽ không xử lý các gói tin gửi đến địa chỉ virtual ip (ngoại trừ gói tin arp) nếu địa chỉ này khác với địa chỉ cổng vật lý của router. vì vậy, trong trường hợp này, chúng ta sẽ không thể thực hiện ping, telnet, ssh đến địa chỉ virtual ip.



- Nếu Master sập (gọi là Router A), xong Backup router khác lên thay (gọi là Router B), xong Router A sống lại…Nếu Router A có Primary IP = Physical IP, hoặc được cấu hình bật preempt mode => Nó sẽ được khôi phục làm Master. Nếu không, nó sẽ giữ ở trạng thái Backup và khôi phục lại Priority cũ.

***

- **Cơ chế VRRP trong mô hình HA Keepalived**
  - Virtual router đại diện cho một nhóm thiết bị sẽ có một virtual IP và một đỉa chỉ **MAC (Media Access Control)** đặc biệt là ***00-00-5E-00-01-XX***. Trong đó, XX là số định danh của router ảo – **Virtual Router Identifier (VRID)**, mỗi virtual router trong một mạng sẽ có một giá trị VRID khác nhau. Vào mỗi thời điểm nhất định, chỉ có một router vật lý dùng địa chỉ MAC ảo này. Khi có ARP request gửi tới virtual IP thì router vật lý đó sẽ trả về địa chỉ MAC này.
 
 
  - Các router vật lý sử dụng chung VIP phải liên lạc với nhau bằng địa chỉ multicast ***224.0.0.18*** bằng giao thức VRRP. Các router vật lý sẽ có độ ưu tiên (priority) trong khoảng từ 1 – 254, và router có độ ưu tiên cao nhất sẽ thành Master, các router còn lại sẽ thành các Slave/Backup, hoạt động ở chế độ chờ.
  
  
  - Các router/server vật lý dùng chung VIP sẽ có 2 trạng thái là **MASTER/ACTIVE** và **BACKUP/SLAVE**. Cơ chế failover được xử lý bởi giao thức VRRP, khi khởi động dịch vụ, toàn bộ các server dùng chung VIP sẽ gia nhập vào một nhóm multicast. Nhóm multicast này dùng để gởi/nhận các gói tin quảng bá VRRP. Các router sẽ thông báo độ ưu tiên (priority) của mình, server với độ ưu tiên cao nhất sẽ được chọn làm MASTER. Một khi nhóm đã có 1 MASTER thì MASTER này sẽ chịu trách nhiệm gởi các gói tin quảng bá VRRP định kỳ cho nhóm multicast.
 
 
  - Nếu vì một sự cố gì đó mà các server BACKUP không nhận được các gói tin quảng bá từ MASTER trong một khoảng thời gian nhất định thì cả nhóm sẽ bầu ra một MASTER mới. MASTER mới này sẽ tiếp quản địa chỉ VIP của nhóm và gởi các gói tin ARP báo là nó đang giữ địa chỉ VIP này. Khi MASTER cũ hoạt động bình thường trở lại thì router này có thể lại trở thành MASTER hoặc trở thành BACKUP tùy theo cấu hình độ ưu tiên của các router.

***
***
## Keepalived Configuration File

- Để cấu hình dịch vụ keepalived, ta cần phải chỉnh sửa file **/etc/keepalived/keepalived.conf**. Như trong Docker thì là **/usr/local/etc/keepalived/keepalived.conf**

***

- **Cấu hình Keepalived cơ bản:**

```
global_defs {
   notification_email {
        admin@gmail.com
   }
   notification_email_from keepalived-web1@gmail.com
   smtp_server x.x.x.x
   smtp_connect_timeout 30
}

vrrp_script chk_haproxy {
	script "<command>"     
	interval <time>
	weight <n>
}

vrrp_instance string {
    state MASTER|BACKUP
    interface <string>
    mcast_src_ip @IP
    virtual_router_id <num>
    priority <num>
    advert_int <num>
    smtp_alert
	 unicast_src_ip @IP
	 unicast_peer {
	    @IPs
	  }
    authentication {
        auth_type PASS|AH
        auth_pass <string>
    }
    virtual_ipaddress { # Block limited to 20 IP addresses
        @IP
        @IP
    }
    notify_master "/path_to_script/script_fault.sh <arg_list>"
    notify_backup "/path_to_script/script_fault.sh <arg_list>"
    notify_fault "/path_to_script/script_fault.sh <arg_list>"
}

virtual_server @IP @Port {
    delay_loop <num>
    lb_algo <string>
    lb_kind <string>
    persistence_timeout 600
    protocol TCP
    real_server @IP @Port {
        weight 100
        TCP_CHECK {
            connect_timeout 10
        }
    }
    real_server @IP @Port {
        weight 100
        TCP_CHECK {
            connect_timeout 10
        }
    }
    …
}
```

- **global\_defs**: cấu hình thông tin toàn cục (global) cho keepalived như việc cho phép các admin xác định chi tiết thông báo khi có các thay đổi đối với bộ cân bằng tải - load balancer state changes ( gửi email thông báo từ ai, tới đâu,… ) hay tên của cluster đang cấu hình 
  - **notification\_email:** những admin sẽ nhận thông báo
  - **notification\_email\_from:** nơi gửi load balancer state changes cho các admin bên trên
  - **smtp\_...**: Cấu hình máy chủ thư để gửi thông báo
 
- **vrrp\_script**: chứa script, lệnh thực thi hoặc đường dẫn tới script kiểm tra dịch vụ (Ví dụ: nếu dịch vụ này down thì keepalived sẽ tự chuyển VIP sang 1 server khác
  - **script "killall -0 haproxy"**: Kiểm tra dịch vụ HAProxy còn hoạt động trên node hay không, nếu không VIP sẽ tự động nhảy sang
  - **interval 3**: Kiểm tra mỗi 3 giây
 
- **vrrp\_instance**: thông tin chi tiết về 1 server vật lý (Master hoặc các Bakup) trong nhóm dùng chung VRRP. Gồm các thông tin như interface dùng để liên lạc của server này, độ ưu tiên để, virtual IP tương ứng với interface, cách thức chứng thực, script kiểm tra dịch vụ….

  - **state (MASTER|BACKUP)**: chỉ trạng thái **MASTER** hoặc **BACKUP** được sử dụng bởi máy chủ. Nếu là **MASTER** thì máy chủ này có nhiệm vụ nhận và xử lý các gói tin từ host đi lên. Nếu **MASTER** sập, những **BACKUP** này sẽ dựa vào 1 cơ chế bầu chọn và lên làm **Master**.
 
  - **interface**: chỉ định cổng mạng nào sẽ sử dụng cho hoạt động IP Failover – VRRP (ràng buộc IP ảo với giao diện mạng vật lý (interface) trên Linux server - real network interface)
 
  - **mcast\_src\_ip**: địa chỉ IP thực của card mạng Interface của máy chủ tham gia vào VRRP. Các gói tin trao đổi giữa các VRRP Router sử dụng địa chỉ thực nà
 
  - **virtual\_router\_id**: định danh cho các Virtual Router thuộc cùng 1 nhóm VRRP. Hiểu nôm na là 1 router có thể tham gia nhiều nhóm VRRP (các nhóm hoạt động độc lập nhau, nhưng các LVS Router tham gia cùng một Virtual Router phải có cùng **virtual\_router\_id**), và VRRP-ID là tên gọi của từng nhóm.
 
  - **priority**: chỉ định độ ưu tiên của VRRP router (tức độ ưu tiên máy chủ dịch vụ trong quá trình bầu chọn MASTER). Các VRRP Router trong cùng một VRRP Group tiến hành bầu chọn Master sử dụng giá trị priority đã cấu hình cho máy chủ đó. Priority có giá trị từ 0 đến 255. Nguyên tắc có bản: Priority cao nhất thì nó là Master, nếu priority bằng nhau thì IP cao hơn là Master. Khi cài đặt ban đầu, cần đảm bảo file cấu hình trên máy Backup thấp hơn trên Master.
 
  - **advert\_int**: thời gian giữa các lần gửi gói tin VRRP advertisement (đơn vị giây), kiểm tra xem node Master còn hoạt động không
 
  - **smtp\_alert**: kích hoạt thông báo bằng email SMTP khi trạng thái MASTER có sự thay đổi.
 
  - **authentication**: chỉ định hình thức xác thực trong VRRP. Dùng để xác thực máy chủ dự phòng trong trường hợp failover. ‘auth\_type‘, sử dụng hình thức mật khẩu plaintext hay mã hoá AH. ‘auth\_pass‘, chuỗi mật khẩu chỉ chấp nhận 8 kí tự
 
  - **virtual\_ipaddress**: Địa chỉ IP ảo (VIP) của nhóm VRRP đó (Chính là địa chỉ dùng làm gateway cho các host). Các gói tin trao đổi, làm việc với host đều sử dụng địa chỉ ảo này. Có thể thêm nhiều nhưng tránh trùng với DHCP server
 
  - **notify\_master**: chỉ định chạy shell script nếu có sự kiện thay đổi về trạng thái MASTER.
  - **notify\_backup**: chỉ định chạy shell script nếu có sự kiện thay đổi về trạng thái BACKUP.
  - **notify\_fault**: chỉ định chạy shell script nếu có sự kiện thay đổi về trạng thái thất bại (fault).
 
  - **unicast\_src\_ip:** Địa chỉ IP riêng của server đang cấu hình (my ip - on real network), hay còn gọi là  địa chỉ nguồn - nơi gửi các gói VRRP
  - **unicast\_peer:** Địa chỉ IP riêng của (các) server khác (peer ip - on real network), hay còn gọi là khối địa chỉ IP của máy chủ - nơi các gói VRRP sẽ được gửi đến.



- **virtual\_server:** Thông tin các máy chủ ảo, giống nhau trên tất cả các LBx
  - **delay\_loop:** Kiểm tra trạng thái (health check) của RealServer sau mỗi x giây
  - **lb\_algo:** thuật toán cân bằng tải - option specifies the kind of algorithm used for availabilit
  - **lb\_kind**: Phương thức định tuyến (DR - Direct Route, NAT - Network Address Translatio,...)
  - **Persistence\_timeOut 60**: Giữ/Hướng kết nối một máy khách cụ thể đến cùng một RealServer trong khoảng thời gian 60s. Mối yêu cầu kết nối mới từ máy khách sẽ reset giá trị thời gian
  - **protocol TCP**: Kiểm tra trạng thái RealServer với Giao thức TCP
  - **real\_server:** cấu hình các RealServer
  - **TCP\_CHECK:** Kiểm tra tính khả dụng của máy chủ thực sử dụng TCP

***

- **Ví dụ một file cấu hình Keepalived đơn giản:**

**MASTER:**

```
global_defs {
  router_id LVS_MAIN
}

vrrp_instance VI_1 {
  state MASTER
  priority 150
  advert_int 1
  virtual_router_id 51
  interface ens33

  unicast_src_ip 192.168.0.24/24
  unicast_peer {
    192.168.0.66/24
  }

  virtual_ipaddress {
    192.168.0.150/24
  }

  authentication {
    auth_type PASS
    auth_pass d0ck3r
  }

  notify "/container/service/keepalived/assets/notify.custom.sh"
}

virtual_server 192.168.0.150 80 {
    delay_loop 5
    lb_algo wlc
    lb_kind NAT
    persistence_timeout 600
    protocol TCP

    real_server 172.20.0.50 80 {
        weight 100
        TCP_CHECK {
            connect_timeout 10
        }
    }

    real_server 172.20.0.60 80 {
        weight 100
        TCP_CHECK {
            connect_timeout 10
        }
    }
}

virtual_server 192.168.0.150 8080 {
    delay_loop 5
    lb_algo wlc
    lb_kind NAT
    persistence_timeout 600
    protocol TCP

    real_server 172.20.0.50 8080 {
        weight 100
        TCP_CHECK {
            connect_timeout 10
        }
    }

    real_server 172.20.0.60 8080 {
        weight 100
        TCP_CHECK {
            connect_timeout 10
        }
    }
}
```

**BACKUP:**

```
global_defs {
  router_id LVS_BCKP
}

vrrp_instance VI_1 {
  state BACKUP
  priority 100
  advert_int 1
  virtual_router_id 51
  interface ens33

  unicast_src_ip 192.168.0.66/24
  unicast_peer {
    192.168.0.24/24
  }

  virtual_ipaddress {
    192.168.0.150/24
  }

  authentication {
    auth_type PASS
    auth_pass d0ck3r
  }

  notify "/container/service/keepalived/assets/notify.custom.sh"
}

virtual_server 192.168.0.150 80 {
    delay_loop 5
    lb_algo wlc
    lb_kind NAT
    persistence_timeout 600
    protocol TCP

    real_server 172.20.0.50 80 {
        weight 100
        TCP_CHECK {
            connect_timeout 10
        }
    }

    real_server 172.20.0.60 80 {
        weight 100
        TCP_CHECK {
            connect_timeout 10
        }
    }
}

virtual_server 192.168.0.150 8080 {
    delay_loop 5
    lb_algo wlc
    lb_kind NAT
    persistence_timeout 600
    protocol TCP

    real_server 172.20.0.50 8080 {
        weight 100
        TCP_CHECK {
            connect_timeout 10
        }
    }

    real_server 172.20.0.60 8080 {
        weight 100
        TCP_CHECK {
            connect_timeout 10
        }
    }
}
```

![temp](https://user-images.githubusercontent.com/43572616/179472547-1c3d8544-4fee-4ef5-9d45-f85268edb033.png)
