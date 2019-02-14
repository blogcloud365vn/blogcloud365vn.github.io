---
date: 2019-02-13
title: Hướng dẫn tạo virtualhost trong nginx
categories:
  - Linux
description: Hướng dẫn cài đặt web server dùng Nginx
author: quyenbx
tags: [Nginx, Linux, CentOS7]
type: Document
---

Vhost viết tắt của virtual host là kỹ thuật cho phép nhiều website có thể chia sẻ chung một IP.Trong phần này, chúng ta sẽ tập trung viết về cấu hình vhost cho nginx và cấu hình php-fpm để vhost xử lý được file php
Để xem cách cài đặt nginx bạn có thể xem lại bài trước [tại đây](https://blog.cloud365.vn/linux/cai-dat-nginx/)

## 1. Mô hình cài đặt
Webserver: Chuẩn bị một máy chủ cài đặt OS Centos 7.6.1810 có địa chỉ IP public 10.10.11.165

Máy Client: Chuẩn bị một máy client cài đặt OS Win 10 để kiểm tra truy cập website với IP client 10.10.11.160

![](/images/img-nginx/mohinh.png)

## 2. Cài đặt php
Để Vhost có thể xử lý được file php chúng ta cần cài đặt php, php-fpm và các extention liên quan.Chúng ta truy cập ssh vào máy chủ webserver và chạy lệnh cài đặt bên dưới

```sh
yum install -y php-common php-bcmath php-cli php-devel php-mcrypt php-mysql php-password-compat php-pclzip php-pdo php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc php-dba php-embedded php-enchant php-mbstring php-intl libssh2 php-pecl-ssh2 php-pecl-memcached php-pecl-memcache php-fpm
```

Sửa file cấu hình của php-fpm

```sh
sed -i 's/user = apache/user = nginx/g' /etc/php-fpm.d/www.conf
sed -i 's/group = apache/group = nginx/g' /etc/php-fpm.d/www.conf
service php-fpm restart
```

## 3. Cấu hình Virtual Host
Truy cập vào đường dẫn chứa file config của nginx
```sh
cd /etc/nginx/
```

Backup lại file nginx.conf
```sh
mv nginx.conf nginx.conf.bak
```

Sửa file cấu hình Nginx
```sh
cat >> nginx.conf << "EOF"
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
EOF
```
<a name="vhost"></a>
Truy cập vào đường dẫn chung chứa code của các website và tạo thư mục riêng chứa code của site quyenbx.name.vn
```sh
cd /var/www/html
mkdir quyenbx.name.vn
```

Truy cập đường dẫn chứa các file cấu hình của virtualhost
```sh
cd /etc/nginx/conf.d/
```

Backup file cấu hình default
```sh
mv default.conf default.conf.bak
```

Tạo virtualhost trong Nginx
```sh
cat >> quyenbx.name.vn.conf << "EOF"
server {
    listen       80;
    server_name  quyenbx.name.vn;
    access_log /var/logs/nginx/quyenbx.name.vn-access_log;
    error_log /var/logs/nginx/quyenbx.name.vn-error_log;

location / {
        root   /var/www/html/quyenbx.name.vn;
        index  index.html index.php index.htm;
    }

location ~ \.php$ {
        root           /var/www/html/quyenbx.name.vn;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
EOF
```

Tạo thư mục chứa log cho Vhost
```sh
mkdir /var/logs/nginx
```

Restart lại service nginx
```sh
service nginx restart
```

Trong bài viết này sử dụng domain quyenbx.name.vn để cấu hình virtualhost, người dùng cần thay đổi tên domain cũng như đường dẫn chứa code phù hợp

Truy cập đường dẫn chứa code của site quyenbx.name.vn
```sh
cd /var/www/html/quyenbx.name.vn
```

Chúng ta tiến hành tạo 1 file info.php trong thư mục /var/www/html/quyenbx.name.vn để kiểm tra website
```sh
cat >> info.php << "EOF"
<?php
phpinfo();
?>
EOF
```

Sau đó chúng ta truy cập đường dẫn http://quyenbx.name.vn/info.php trả về kết quả như hình bên dưới

![](/images/img-nginx/info1.png)

LƯU Ý: Thực thế người dùng có domain đăng ký ở các nhà cung cấp ví dụ [Nhân Hòa](https://nhanhoa.com/trang/ten-mien/bang-gia-ten-mien.html) thì cần trỏ bản ghi cho tên miền về IP của server website để có thể truy cập trên trình duyệt web.Trong bài viết này mình sử dụng add file hosts trong windows để truy cập

## 4. Thêm một Virtual Host khác trên server Web
Để thêm một Vhost khác trên server Website chúng ta thao tác lại các bước bên dưới.Ví dụ mình muốn thêm một website khác trên server này với tên thangth.name.vn

Truy cập vào đường dẫn chung chứa code của các website và tạo thư mục riêng chứa code của site thangth.name.vn
```sh
cd /var/www/html
mkdir thangth.name.vn
```

Truy cập đường dẫn chứa các file cấu hình của virtualhost
```sh
cd /etc/nginx/conf.d/
```

Tạo virtualhost trong Nginx
```sh
cat >> thangth.name.vn.conf << "EOF"
server {
    listen       80;
    server_name  thangth.name.vn;
    access_log /var/logs/nginx/thangth.name.vn-access_log;
    error_log /var/logs/nginx/thangth.name.vn-error_log;

location / {
        root   /var/www/html/thangth.name.vn;
        index  index.html index.php index.htm;
    }

location ~ \.php$ {
        root           /var/www/html/thangth.name.vn;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
EOF
```

Restart lại service nginx
```sh
service nginx restart
```

Truy cập đường dẫn chứa code của site thangth.name.vn
```sh
cd /var/www/html/thangth.name.vn
```

Chúng ta tiến hành tạo 1 file test.php trong thư mục /var/www/html/thangth.name.vn để kiểm tra website
```sh
cat >> test.php << "EOF"
<?php
echo "WebSite ThangTh";
?>
EOF
```

Sau đó chúng ta truy cập đường dẫn http://thangth.name.vn/test.php trả về kết quả như hình bên dưới

![](/images/img-nginx/test.png)

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>


