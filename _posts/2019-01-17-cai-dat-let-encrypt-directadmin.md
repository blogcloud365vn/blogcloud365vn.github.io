---
date: 2019-01-17
title: Hướng dẫn cài đặt Let's Encrypt trên DirectAdmin
categories:
  - Linux
description: Tài liệu hướng dẫn cài đặt Let's Encrypt trên DirectAdmin
author: tuanda
tags: [DirectAdmin, Linux, CentOS7]
type: Document
---

Bài viết này sẽ hướng dẫn cách cài đặt SSL với Let's Encrypt trên VPS/Server của Cloud365 và sử dụng control DirectAdmin.
![](/images/img-letencrypt-da/image1.png)
## Bước 1: Enable Let's Encrypt trên DirectAdmin 
Kiểm tra và update version DirectAdmin lên bản 1.5 hoặc cao hơn
```sh
/usr/local/directadmin/directadmin v
```
![](/images/img-letencrypt-da/image2.png)

Bật tính năng Let's Encrypt và SNI trên DirectAdmin
```sh
echo "letsencrypt=1" >> /usr/local/directadmin/conf/directadmin.conf
echo "enable_ssl_sni=1" >> /usr/local/directadmin/conf/directadmin.conf
```

Khởi động lại dịch vụ DirectAdmin
```sh
/etc/init.d/directadmin restart
```

Update license Let's Encrypt
```sh
wget -O /usr/local/directadmin/scripts/letsencrypt.sh http://files.directadmin.com/services/all/letsencrypt.sh
```

Update web-server configs trên DirectAdmin
```sh
cd /usr/local/directadmin/custombuild
./build update
./build letsencrypt
./build rewrite_confs
```

## Bước 2: Thực hiện trỏ domain
Cần thực hiện trỏ domain cần cài SSL về IP của hệ thống (sử dụng control DirectAdmin) trong đó bao gồm cả record www.

## Bước 3: Cấu hình SSL cho domain
Trong giao diện người dùng `(User Level)` trên control DirectAdmin, chọn `SSL Certificates`:
![](/images/img-letencrypt-da/image3.png)

Điền đầy đủ các thông tin như hình dưới và chọn 
![](/images/img-letencrypt-da/image4.png)

`Save`

`Common Name`: Điền domain cần cài đặt và cấu hình SSL

`Key Size (bits)`: Chọn 2048 hoặc 4096

`Certificate Type`: Chọn SHA256

`Force SSL with https redirect`: Bắt buộc sử dụng HTTPS khi truy cập tên miền trên.

Sau khi nhấn `Save` sẽ xuất hiện thông báo như hình dưới đây có nghĩa là quá trình cài đặt và cấu hình SSL cho domain đã thành công.
![](/images/img-letencrypt-da/image6.png)

Quay về `Home`, trong giao diện người dùng Directadmin, click chọn `SSL Certificates`:
![](/images/img-letencrypt-da/image7.png)
`SSL is currently enabled for this domain`: SSL đang được bật đối với tên miền này.
Click vào chữ `here`: 
![](/images/img-letencrypt-da/image8.png)
Chọn `Use a symbolic link from private_html to public_html - allows for same data in http and https` để nội dung trong thư mục `private_html` đồng bộ với `public_html`
Chọn `Force SSL with https redirect` để buộc chuyển hướng vào site có HTTPS

## Bước 4: Kiểm tra kết quả
Vào trình duyệt để kiểm tra trạng thái của site không còn cảnh báo và certificate hợp lệ:
![](/images/img-letencrypt-da/image19.png)

---
Thực hiện bởi [cloud365.vn](https://cloud365.vn/)