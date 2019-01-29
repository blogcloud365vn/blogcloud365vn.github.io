---
title: Hướng dẫn sử dụng ngrok
categories:
  - Linux
description: Tài liệu về Linux
author: datpt
tags: [ngrok, Linux]
type: Document
---

**Lời mở đầu**

- Ngrok là gì, đơn giản là công cụ “phơi” máy chủ ở đằng sau tường lửa ra ngoài mạng internet thông qua một đường hầm bảo mật

- Bạn có thể tải về và chạy chương trình trên máy tính và cung cấp cho nó cổng mạng dịch vụ, thường là cổng của web server.
Nó sẽ kết nối đến dịch vụ đám mây ngrok, dịch vụ này sẽ nhận các lưu lượng dữ liệu trên một địa chỉ công cộng và chuyển tiếp lưu lượng truy cập thông qua quá trình ngrok chạy trên máy tính của bạn và sau đó đến địa chỉ cục bộ bạn chỉ định.


## 1. Một số  tiện ích của ngrok mang lại.

- Đường hầm bảo mật: ví dụ tạo một HTTPS URL công khai cho web site đang chạy cục bộ.

- Không cần có thêm cổng chuyển tiếp: Không cấu hình cổng chuyển tiếp trên router hoặc lãng phí thời gian thiết lập các giải pháp DNS động. Ngrok hoạt động ở mọi nơi mà không có thay đổi, ngay cả khi một thiết bị thay đổi mạng. 

- Mật khẩu được bảo vệ: Đặt thông tin đăng nhập auth http để bảo vệ quyền truy cập vào đường hầm của bạn và những người bạn chia sẻ nó.

- Đường hầm TCP: Phơi bày bất kỳ dịch vụ nối mạng nào với internet, thậm chí những trang không sử dụng HTTP như SSH.

- Hỗ trợ Websocket: Chia sẻ ứng dụng web thời gian thực.

- Nhiều đường hầm đồng thời: Chạy nhiều đường hầm đồng thời với một ngrok client.

- Replay Webhook Requests: Dễ dàng phát triển tích hợp webhook bằng cách chỉ đơn giản là 'phát lại' yêu cầu webhook đến máy chủ của bạn.

## 2. Cách cài đặt (Trên CentOS 7).

Cài đặt wget và unzip :

```sh
yum install -y wget unzip
```

Tải bản cài đặt `ngrok` :

```sh
wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
```

Giải nén `ngrok` :

```sh
unzip ngrok-stable-linux-amd64.zip
```

Sau khi giải nén xong , dùng lệnh sau để xem chi tiết về các lệnh được sử dụng bởi `ngrok` :

```sh
./ngrok help
```

![](/images/img-ngrok/ngrok_help.png)

## 3. Cách sử dụng ngrok.

- Ở đây mình sẽ ví dụ với 2 dịch vụ là web server và ssh, các dịch vụ khác chúng ta thực hiện tương tự.

### 3.1. Phơi dịch vụ web ra ngoài internet sử dụng ngrok.

Cài đặt dịch vụ httpd:

```sh
yum install -y httpd
```

Khởi động dịch vụ :

```sh
systemctl start httpd
systemctl enable httpd
```

Tạo file `index.html` :

```sh
echo "<h1>Performed by Cloud365</h1>" >> /var/www/html/index.html
```

Khởi tạo một tunel để dịch vụ http có thể ra ngoài internet :

```sh
./ngrok http 80
```

Kết quả thu được như sau :

![](/images/img-ngrok/ngrok_httpd.png)

- Như vậy là chúng ta đã tạo thành công một tunel để web server có thể truy cập được từ internet.

- Truy cập vào web server để kiểm tra theo 2 đường link forward ở trên.

- Kết quả :

![](/images/img-ngrok/ng_httpd_hq.png)

### 3.2. Phơi dịch vụ SSH ra ngoài internet sử dụng ngrok.

Để tạo được một tunel ssh chúng ta cần phải đăng nhập vào tài khoản của `ngrok`. Để đăng nhập, chúng ta vào đường [link này](https://dashboard.ngrok.com/user/login) .

SSH sử dụng port mặc định là 22, mình cũng sử dụng port đó nên sẽ mở tunnel thông qua port 22 với giao thức tcp:

```sh
./ngrok tcp 22
```

Thông tin nhận được như sau :

![](/images/img-ngrok/ngrok_ssh.png)

- Trong đó :

    - 0.tcp.ngrok.io => là host để ssh.

    - 11721 => là port ssh.

Chúng ta tiến hành ssh thử với các thông tin như bên trên:

![](/images/img-ngrok/ngrok_ssh_hq.png)

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>