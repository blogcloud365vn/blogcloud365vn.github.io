---
date: 2019-01-17
title: Hướng dẫn cài đặt Wordpress cơ bản trên DirectAdmin
categories:
  - Linux
description: Tài liệu hướng dẫn cài đặt Wordpress cơ bản trên DirectAdmin
author: tuanda
tags: [DirectAdmin, Linux, CentOS7]
type: Document
---
Bài viết hướng dấn cách cài đặt cơ bản 1 site wordpess trên DirectAdmin:

## Bước 1: Download bộ cài Wordpress

```sh
cd home/cloud365/domains/<domains>/public_html/
```
Thực hiện lệnh:
```sh
rm -rf /home/cloud365/domains/<domains>/public_html/*
```

Download bộ cài và giải nén:
```sh
wget https://wordpress.org/latest.tar.gz`
tar xvfz latest.tar.gz
```

Sau khi giải nén sẽ có 1 thư mục có tên `wordpress`, copy toàn bộ file trong thư mục này vào thư mục: `/home/cloud365/domains/<domains>/public_html/`
Lưu ý điều chỉnh lại permission và owner chính xác:
![](/images/img-letencrypt-da/image10.png)

## Bước 2: Tạo database cho site

Ở User level truy cập vào `MySQL Management`:
![](/images/img-letencrypt-da/image11.png)

Chọn `create new Database`
![](/images/img-letencrypt-da/image12.png)

Nhập database name, user name, password:
![](/images/img-letencrypt-da/image13.png)

Sau khi tạo database thành công sẽ có thông báo sau:
![](/images/img-letencrypt-da/image14.png)

## Bước 3: Cấu hình cho site Wordpress
Sau khi trỏ tên miền về VPS, truy cập vào http://<domain> sẽ hiển thị như sau:
![](/images/img-letencrypt-da/image15.png)

Chọn `Continue`-> `Let's go`
Điền thông tin database đã tạo ở trên:
![](/images/img-letencrypt-da/image16.png)

Nhập thông tin của site muốn cài đặt:
![](/images/img-letencrypt-da/image17.png)

Hiện ra thông báo sau là trang đã được cài đặt thành công, bấm vào Login để đăng nhập quản trị:
![](/images/img-letencrypt-da/image18.png)

---
Thực hiện bởi [cloud365.vn](https://cloud365.vn/)