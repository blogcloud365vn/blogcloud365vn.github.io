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
Bước 1: Kiểm tra và update version DirectAdmin lên bản 1.5 hoặc cao hơn

Kiểm tra version của control DirectAdmin
```sh
/usr/local/directadmin/directadmin v
```
![](/images/img-letencrypt-da/image2.png)
Bước 2: Bật tính năng Let's Encrypt trên DirectAdmin
```sh
echo "letsencrypt=1" >> /usr/local/directadmin/conf/directadmin.conf
```
Bước 3: Bật SNI trên DirectAdmin
```sh
echo "enable_ssl_sni=1" >> /usr/local/directadmin/conf/directadmin.conf
```
Bước 4: Khời động lại dịch vụ DirectAdmin
```sh
/etc/init.d/directadmin restart
```
Bước 5: Update license Let's Encrypt
```sh
wget -O /usr/local/directadmin/scripts/letsencrypt.sh http://files.directadmin.com/services/all/letsencrypt.sh
```
Bước 6: Update web-server configs trên DirectAdmin
```sh
cd /usr/local/directadmin/custombuild
./build update
./build letsencrypt
./build rewrite_confs
```
Bước 7: Thực hiện trỏ domain cần cài đặt và cấu hình SSL về IP của hệ thống sử dụng control DirectAdmin (bao gồm cả record www).

Bước 8: Trong giao diện người dùng `(User Level)` trên control DirectAdmin, chọn `SSL Certificates`:
![](/images/img-letencrypt-da/image3.png)

Bước 9: Điền đầy đủ các thông tin như hình dưới và chọn 
![](/images/img-letencrypt-da/image4.png)
`Save`

`Common Name`: Điền domain cần cài đặt và cấu hình SSL

`E-Mail`: Điền địa chỉ email để quản lý SSL

`Key Size (bits)`: Chọn 2048 hoặc 4096

`Certificate Type`: Chọn SHA256






















































---
Thực hiện bởi [cloud365.vn](https://cloud365.vn/)