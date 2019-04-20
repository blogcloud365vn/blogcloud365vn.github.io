---
date: 2019-04-19
title: Hướng dẫn cơ bản sử dụng công cụ Easy Engine (Phần 2)
categories:
  - Linux
description: Hướng dẫn cơ bản sử dụng công cụ Easy Engine (Phần 2)
author: tuanda
tags: [Linux]
type: Document
set: Gioi-thieu-EE
set_order: 19
---

## Các câu lệnh quản lý website cơ bản

Sau khi tìm hiểu và làm quen với công cụ Easy Engine, các bạn đã nắm được sơ bộ các khái niệm, cũng như vị trí các file cấu hình, log. Bài viết này sẽ tập trung vào các hướng dẫn thao tác cơ bản bằng câu lệnh đối với 1 website sử dụng Easy Engine.

### Lệnh truy cập thư mục website

Để truy cập vào thư mục gốc của một website nào đó sẽ cần dùng lệnh `ee site cd `như sau: 

```sh
ee site cd domain.com
```

### Xóa website 

Có thể xoá toàn bộ dữ liệu của một website với lệnh `ee site delete`:

```sh
ee site delete domain.com
```

Hoặc nếu chỉ cần xoá mã nguồn thì thêm tham số `--files` vào, hoặc nếu chỉ cần xoá database thì thêm tham số `--db` vào. Ví dụ:

```sh
ee site delete domain.com --files #Xóa dữ liệu web của domain này
```

### Tắt website

Nếu cần tắt một website mà không phải xoá dữ liệu thì có thể sử dụng lệnh sau: `ee site disable`.

```sh
ee site disable domain.com
```

Còn nếu muốn bật lên thì sử dụng ee site enable.

```sh
ee site enable domain.com
```

### Sửa cấu hình Nginx của website

Để sửa cấu hình NGINX của một website nào đó (tại `/etc/nginx/sites-available`) thì chỉ cần sử dụng lệnh `ee site edit`.

```sh
ee site edit domain.com
```

Có thể sẽ cần hỏi cần dùng chương trình soạn thảo văn bản nào ở lần đầu tiên sử dụng, hãy chọn số 2 để sử dụng `Nano` cho đơn giản. Sau khi sửa và lưu lại, hệ thống sẽ tự động test và restart lại NGINX.

### Xem thông tin website

Nếu cần xem thông tin website như thông tin database, các software sử dụng…thì sử dụng lệnh `ee site info`.

```sh
ee site info domain.com
```

### Xem danh sách website

Chỉ cần sử dụng lệnh `ee site list` là sẽ xem được danh sách các website có trên máy chủ.

###	Cập nhật thông tin website

Đây là một lệnh rất hữu ích nếu cần bổ sung tính năng nào đó vào website mà lúc thêm vào chưa cài cho, ví dụ muốn đổi từ cấu hình W3 Total Cache sang WP Super Cache thì sẽ sử dụng lệnh này.

```sh
ee site update domain.com --wpfc
```

Ở lệnh trên, nghĩa là mình cần cập nhật cho website domain.com sử dụng WordPress với cấu hình FastCGI Cache.

### Lệnh xóa cache

Lệnh xoá cache này được sử dụng để xoá cache của Pagespeed, Memcached và Opcache. Cách sử dụng chỉ cần gõ lệnh `ee clean --all` là được.

## Các lệnh xem log hệ thống và website

Để xem log có thể truy cập vào các tập tin đã trình bày ở phần Vị trí các tập tin cấu hình để xem, nhưng trong EasyEngine có tích hợp sẵn những lệnh đơn giản để xem log nhanh. Các lệnh xem log của EasyEngine giống như xem live log bằng lệnh `tail` trong Linux.

Khi xem log, nếu muốn thoát ra thì dùng tổ hợp phím Ctrl + C.

### Xem log toàn bộ hệ thống

Để xem toàn bộ log của hệ thống (bao gồm MySQL, PHP và access log) thì có thể sử dụng lệnh đơn giản.

```sh
ee log show
```

Hoặc nếu muốn xem log cụ thể thì thêm tham số `--mysql`, `--php`, `--nginx`, `--fpm` hoặc `--access` vào.

```sh
ee log show --php
```

### Xem log từng domain cụ thể

Nếu có nhiều domain trên máy chủ và cần xem log của một domain nào đó thì có thể thêm tham số domain cần xem log vào. Ví dụ:

```sh
ee log show domain.com
```
hoặc

```sh
ee log show domain.com --fpm
```

Sau khi cài đặt EasyEngine như các lệnh của mình thì sẽ được cung cấp sẵn một số công cụ quản lý máy chủ thông qua UI như xem thống kê Memcached, xoá NGINX FastCGI Cache, phpMyAdmin,…và trang quản trị này sẽ truy cập thông qua cổng 22222, nghĩa là sẽ truy cập vào website với địa chỉ https://IP:22222.

## Cài đặt công cụ admin và PHPmyadmin

Trước tiên để sử dụng được các công cụ admin trong đây thì phải cài vào, chạy lệnh dưới đây để cài:

```sh
ee stack install --utils --phpmyadmin
```

Bây giờ có thể truy cập sử dụng các công cụ đó tại địa chỉ https://IP:22222. Tuy nhiên để vào được trang này phải có mật khẩu bảo vệ và xem tiếp bên dưới để thiết lập mật khẩu bảo vệ.

### Tạo mật khẩu bảo vệ

Để có thể truy cập vào khu vực Admin Tools sẽ cần phải nhập đúng username và mật khẩu bảo vệ, sẽ cần tạo ra với lệnh:

```sh
ee secure --auth
```

Sau đó nhập username và mật khẩu cần tạo ra. Bây giờ có thể vào trang https://IP:22222 với username và mật khẩu vừa thiết lập. Có thể bỏ qua với cảnh báo SSL.
 
### Đổi cổng admin

Nếu không thích cổng 22222 làm cổng vào Admin Tools thì có thể đổi với lệnh.

```sh
ee secure --port 1234
```

Trong đó, 1234 là cổng cần đổi sang.

### Cách truy cập PHPmyadmin

Để truy cập vào phpMyAdmin, có thể vào với đường dẫn https://IP:22222//db/pma/, có thể đăng nhập vào bằng database username và database password của từng website (xem file `wp-config.php` sẽ có thông tin này).

Hoặc có thể đăng nhập vào bằng user root trong MySQL Server. Để lấy được mật khẩu của user root trong MySQL, sử dụng lệnh này sẽ thấy.

```sh
cat /etc/mysql/conf.d/my.cnf
```

### Chuyển site về VPS

BƯỚC 1. THÊM DOMAIN

Trước tiên, cần thêm domain của website cần chuyển về bằng lệnh của EasyEngine.

```sh
ee site create domain.com --wpredis
```

Sau đó truy cập vào thư mục chứa dữ liệu của domain vừa thêm vào với 2 lệnh sau:

```sh
ee site cd domain.com
cd htdocs
```

BƯỚC 2. XÓA DỮ LIỆU Ở WEBSITE TẠO SẴN

Lệnh ở trên sẽ tạo sẵn database và chứa sẵn dữ liệu, nên xóa toàn bộ dữ liệu có sẵn của để nhập dữ liệu cũ vào:

```sh
wp db reset --allow-root
```

Và xóa toàn bộ mã nguồn được cài sẵn:
```
rm -rf *
```

Lệnh trên rất nguy hiểm, hãy chắc chắn là đã truy cập vào thư mục của website mới sử dụng.

**BƯỚC 3. MANG DỮ LIỆU CỦA WEBSITE CŨ VỀ**

Mang toàn bộ dữ liệu mã nguồn của website vào thư mục `/var/www/domain.com/htdocs`, bao gồm tập tin database (.sql).
Sau đó, hãy đổi tên tập tin `wp-config.php` của mã nguồn website cũ thành tên gì đó, miễn không phải là wp-config.php.
```sh
mv wp-config.php wp-config.old.php
```
Nếu máy chủ cũ dùng EasyEngine thì không mang tập tin `wp-config.php` theo.

**BƯỚC 4. IMPORT DATABASE**

Bây giờ hãy nhập các dữ liệu database của website cũ vào database của website đã thêm ở EasyEngine bằng lệnh wp như sau:

```sh
wp db import data.sql --allow-root
```

Trong đó, data.sql là tên tập tin .sql đã upload lên thư mục htdocs. Đây chính là tập tin database ở website cũ mà đã chuyển về.

**BƯỚC 5. KIỂM TRA `WP-CONFIG.PHP`**

Bây giờ, website sẽ sử dụng tập tin `wp-config.php` tại `/var/www/domain.com/`.

Lưu ý là nếu có sử dụng table prefix khác với wp_ mặc định thì hãy sửa lại phần $table_prefix trong file wp-config.php để tránh lỗi WordPress  bắt cài lại.

**BƯỚC 6. PHÂN QUYỀN LẠI THƯ MỤC**

Để tránh tình trạng bị hỏi thông tin FTP khi cài hoặc cập nhật gì đó trên WordPress thì phải phân quyền lại để đảm bảo các tập tin và thư mục của website vừa chuyển về thuộc sở hữu của user www-data và group www-data.

```sh
chown -R www-data:www-data /var/www/domain.com
```

**BƯỚC 7. CÀI PLUGIN CẦN THIẾT**

Sau khi chuyển xong, nên chạy lệnh `ee site update` để tự động cài các plugin cache và thiết lập cho chính xác với cấu hình đang sử dụng. 

Đầu tiên là đổi cấu hình của tên miền sang một cấu hình khác và sau đó dùng lại lệnh này để chuyển về cấu hình mong muốn. Ví dụ đang dùng `--wpredis` thì sẽ chuyển sang `--wpfc` rồi chuyển ngược về lại `--wpredis`.

```sh
ee site update domain.com --wpfc
```

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>