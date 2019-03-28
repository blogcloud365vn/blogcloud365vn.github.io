---
title: Cài đặt OwnCloud trên Ubuntu18
categories:
  - Linux
description: Cài đặt OwnCloud trên Ubuntu18
author: canhdx
tags: [Beginer, Linux,  Other]
type: Document
---

## OwnCloud 

ownCloud là một phần mềm mã nguồn mở được sử dụng cho các dịch vụ lưu trữ tệp. Về mặt chức năng của ownCloud có những điểm tương đồng với Dropbox.

## Chuẩn bị môi trường cài đặt 

1 Server
- OS: Ubuntu18
- RAM: >= 1 GB
- Disk: >= 10 GB
- CPU: >= 1 Core
- Yêu cầu có kết nối internet để cài đặt 
- Update và cài đặt openssh-server
```sh 
sudo apt-get update -y 
sudo apt-get upgrade -y 
sudo apt-get dist-upgrade -y
```

<p align="center">
<img src="/images/img-owncloud/owncloud.png">
</p>

## Cài đặt httpd 

Cài đặt 
```sh 
sudo apt install apache2 -y 
```

Start và enable 
```sh 
sudo systemctl start apache2
sudo systemctl enable apache2
```

Chỉnh sửa firewall cho phép traffic của web đi qua 

Kiểm tra `ufw` 
```sh 
sudo ufw app list
```

Kết qủa 
```sh 
root@ubuntu:~# sudo ufw app list
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH
root@ubuntu:~# 
```

Cho phép traffic trên port 80 và 443 cho httpd 
```sh 
sudo ufw app info "Apache Full"
```

Kết quả 
```sh 
root@ubuntu:~# sudo ufw app info "Apache Full"
Profile: Apache Full
Title: Web Server (HTTP,HTTPS)
Description: Apache v2 is the next generation of the omnipresent Apache web
server.

Ports:
  80,443/tcp
```

Truy cập trình duyệt 
```sh 
http://your_server_ip
```

Kết quả 
<p align="center">
<img src="/images/img-lamp/default_apache.png">
</p>


## Cài đặt MySQL

Cài đặt 
```sh 
sudo apt install mysql-server -y 
```

Cài đặt mật khẩu `root` cho MariaDB 
```sh 
sudo mysql_secure_installation
```

> Mặc đinh mật khẩu root của mysql sẽ để trống. Bước này có thể có hoặc không có tùy nhu cầu của Administrators

Cấu hình DB, user cho `OwnCloud`

- Truy cập mysql 
```sh 
mysql -u root
```

- Tạo DB và user 
```sh 
CREATE DATABASE owncloud;
CREATE USER 'oc_user'@'localhost' IDENTIFIED BY 'YOUR_PASSWORD_HERE';
GRANT ALL PRIVILEGES ON owncloud.* TO 'oc_user'@'localhost';
FLUSH PRIVILEGES;
exit
```

## Cài đặt PHP 

Cài đặt 
```sh 
sudo apt install php libapache2-mod-php php-mysql php-cli
```

> Mặc định repo PHP trên Ubuntu18 sẽ sử dụng php7.2

# Cài đặt OwnCloud 

Add key 
```sh 
curl https://download.owncloud.org/download/repositories/10.0/Ubuntu_18.04/Release.key | sudo apt-key add -
```

Cập nhật source list 
```sh 
echo 'deb http://download.owncloud.org/download/repositories/10.0/Ubuntu_18.04/ /' | sudo tee /etc/apt/sources.list.d/owncloud.list
```

Update và cài đặt các gói bổ sung cho php 
```sh 
sudo apt-get update -y 
sudo apt install php-bz2 php-curl php-gd php-imagick php-intl php-mbstring php-xml php-zip owncloud-files -y 
```

Kiểm tra IP hiện tại của Server 
```sh 
IP=$(ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//')
echo $IP
```

Kiểm tra file vhost mặc định của apache 
```sh 
sudo apache2ctl -t -D DUMP_VHOSTS | grep $IP
```

Kết quả 
```sh 
root@ubuntu:/var/www# sudo apache2ctl -t -D DUMP_VHOSTS | grep 192.168.70.183
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 192.168.70.183. Set the 'ServerName' directive globally to suppress this message
*:80                   192.168.70.183 (/etc/apache2/sites-enabled/000-default.conf:1)
```

Chỉnh sửa VirtualHost cho OwnCloud
```sh 
sed -i "s|/var/www/html|/var/www/owncloud|g" /etc/apache2/sites-enabled/000-default.conf
```

Kiểm tra config 
```sh 
sudo apache2ctl configtest
```

Kết quả OK 
```sh 
...
Syntax OK
```

Reload lại apache
```sh 
sudo systemctl reload apache2
```

Truy cập và Cấu hình OwnCloud 
```sh 
https://server_domain_or_IP
```

Tạo User và password cho tài khoản, Kết nối tài khoản MySQL đã tạo phía trên 
<p align="center">
<img src="/images/img-owncloud/owncloud-u01.png">
</p>

Qúa trình tạo tài khoản đang được thực hiện, nếu có lỗi vui lòng kiểm tra lại tài khoản MySQL 
<p align="center">
<img src="/images/img-owncloud/owncloud-01.1.png">
</p>

Sau khi tạo tài khoản hoàn tất tiến hành đăng nhập 
<p align="center">
<img src="/images/img-owncloud/owncloud-02.png">
</p>

Giao diện đăng nhập 
<p align="center">
<img src="/images/img-owncloud/owncloud-03.png">
</p>

Giao diện người dùng
<p align="center">
<img src="/images/img-owncloud/owncloud-04.png">
</p>

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
