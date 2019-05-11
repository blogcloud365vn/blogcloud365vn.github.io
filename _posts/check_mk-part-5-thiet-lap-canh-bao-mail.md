---
title: CHECKMK part 5 - Cấu hình cảnh báo qua mail
categories:
  - Monitor
description: Tìm hiểu về omd-checkmk.
author: datpt
tags: [monitoring, Linux]
type: Document
set: Gioi-thieu-checkmk
set_order: 21
---

## Lời mở đầu

Ở bài trước mình đã hướng dẫn mọi người cách để thiết lập ngưỡng cảnh báo, hôm nay mình sẽ hướng dẫn mọi người cách để khi các dịch vụ tới ngưỡng sẽ có email từ checkmk cảnh báo cho chúng ta.

## Chuẩn bị

## Thực hiện

## Bước 1: Tạo mật khẩu app cho mail.

- Đầu tiên chúng ta cần bật `IMAP`, tại giao diện của Gmail chọn bánh răng sau đó vào cài đặt  :

![omd-36](/images/img-omd/omd-36.png)

- Tại đây, chuyển đến tab `Chuyển tiếp và POP/IMAP`:

![omd-37](/images/img-omd/omd-37.png)

- Bật `IMAP`:

![omd-38](/images/img-omd/omd-38.png)

- Tiếp theo, Chúng ta cần bật xác thực 2 bước cho gmail ở [đây](http://www.google.com/landing/2step/)

- Tạo mật khẩu ứng dụng chúng ta vào [link](https://myaccount.google.com/security), tại đây chọn `App password`:

![omd-39](/images/img-omd/omd-39.png)

- Đặt tên APP rồi ấn `GENERATE`:

![omd-40](/images/img-omd/omd-40.png)

- Lưu lại đoạn mật khẩu để cấu hình:

![omd-41](/images/img-omd/omd-41.png)

## Bước 2: Cài đặt và cấu hình các gói hỗ trợ.

Cài đặt `postfix` và `mailx`:

```sh
yum update
yum install postfix mailx cyrus-sasl cyrus-sasl-plain
```

Tạo file lưu trữ thông tin gmail, với password là password của app tạo ở trên :

```sh
cat > /etc/postfix/sasl_passwd <<EOF
[smtp.gmail.com]:587    username@gmail.com:password
EOF
```

Phân quyền lại file lưu trữ thông tin Gmail:

```sh
chmod 600 /etc/postfix/sasl_passwd
```

Cấu hình Postfix:

```sh
echo '
relayhost = [smtp.gmail.com]:587
smtp_use_tls = yes
smtp_sasl_auth_enable = yes
smtp_sasl_security_options =
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_CAfile = /etc/ssl/certs/ca-bundle.crt
' >> /etc/postfix/main.cf
```

Mở file postfix và sửa lại các thông tin, tại dòng 120, comment thông số :

```sh
#inet_interfaces = all
```

Tại dòng 126, sửa lại thông số như sau :

```sh
inet_protocols = ipv4
```

Sử dụng postmap để xử lý thông tin xác thực Gmail:

```sh
postmap /etc/postfix/sasl_passwd
```

Khởi động lại `postfix`:

```sh
systemctl restart postfix
```

## Cấu hình trên Wato configuration

- Ở ví dụ này mình chỉ sử dụng để gửi cảnh báo cho 1 user, nếu như có nhu cầu phân nhánh gửi cảnh báo thì chúng ta nên chọn `Contact Group` và gửi cảnh báo thông qua `Contact Group` và các nhánh folder của các host.

Tạo user, user này dùng để test nhận mail cảnh báo, tại `WATO Configuration` chọn tab `Users`:

![omd-42](/images/img-omd/omd-42.png)

Chọn `New User`:

![omd-43](/images/img-omd/omd-43.png)

Điền các thông tin như sau, lưu ý cần phải có trường `Email` để có thể nhận mail cảnh báo :

![omd-44](/images/img-omd/omd-44.png)

Cấu hình cảnh báo qua mail, chọn tab `Notifications` :

![omd-45](/images/img-omd/omd-45.png)

Chọn `New rule`:

![omd-46](/images/img-omd/omd-46.png)

Điền các thông tin như sau :

![omd-47](/images/img-omd/omd-47.png)

![omd-48](/images/img-omd/omd-48.png)


Sau đó lưu lại thông tin.

## Kiểm tra

Tại `Views` chọn `Hosts` rồi chọn `All hosts`:

![omd-49](/images/img-omd/omd-49.png)

Chọn nút các búa như hình dưới :

![omd-50](/images/img-omd/omd-50.png)

Chọn `SEND` để gửi cảnh báo :

![omd-51](/images/img-omd/omd-51.png)

Kiểm tra mail xem đã nhận được cảnh báo thử nghiệm hay chưa, nếu nhận được như hình bên dưới là đã thành công rồi nhé .

![omd-52](/images/img-omd/omd-52.png)

## Tổng kết.

Hôm nay mình đã hướng dẫn mọi người cách để có thể gửi cảnh báo thông qua mail, ở các bài tiếp theo mình sẽ hướng dẫn mọi người gửi cảnh báo trên các ứng dụng khác như là Slack và Telegram. Cảm ơn đã truy cập vào Cloud365 !!!


---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>