---
title: "Nextcloud [Part 2] - Cài đặt Nextcloud trên Ubuntu18"
categories:
  - Nextcloud
description: Cài đặt Nextcloud trên Ubuntu18
author: canhdx
tags: [Nextcloud]
type: Document
set: Nextcloud
set_order: 27
---

## NextCloud 

<p align="center">
<img src="/images/img-nextcloud/nextcloud-logo.jpeg">
</p>

Ở bài viết trước chúng ta đã cài đặt trên Hệ điều hành CentOS7. Bài hướng dẫn này sẽ hướng dẫn các bạn cài đặt trên Ubuntu18

## Chuẩn bị môi trường cài đặt 

1 Server
- OS: Ubuntu18
- RAM: >= 1 GB
- Disk: >= 10 GB
- CPU: >= 1 Core
- Yêu cầu có kết nối internet để cài đặt 
- Login và thao tác với user `root`
- Update và cài đặt openssh-server
```sh 
sudo apt-get update -y 
sudo apt-get upgrade -y 
sudo apt-get dist-upgrade -y
```

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

- Kết qủa 
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

- Kết quả 
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

Cấu hình DB, user cho `NextCloud`

- Truy cập mysql 
```sh 
mysql -u root
```

- Tạo DB và user 
```sh 
CREATE DATABASE nextcloud;
CREATE USER 'nc_user'@'localhost' IDENTIFIED BY 'YOUR_PASSWORD_HERE';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nc_user'@'localhost';
FLUSH PRIVILEGES;
exit
```

## Cài đặt PHP 

Cài đặt 
```sh 
sudo apt install -y php libapache2-mod-php php-ldap php-mysql php-cli php-bz2 php-curl php-gd php-imagick php-intl php-mbstring php-xml php-zip
```

> Mặc định repo PHP trên Ubuntu18 sẽ sử dụng php7.2

# Cài đặt NextCloud 

Phiên bản hiện tại 15.0.7	
Download 
```sh 
wget https://download.nextcloud.com/server/releases/nextcloud-15.0.7.zip -O /opt/nextcloud.zip
```

Giải nén 
```sh 
apt-get install unzip -y 
unzip /opt/nextcloud.zip -d /var/www/
rm -f /opt/nextcloud.zip
```

Cấu hình Virtual host 
```sh 
cat << EOF >> /etc/apache2/sites-available/nextcloud.conf

<VirtualHost *:80>
    ServerAdmin admin@localhost.local
    DocumentRoot /var/www/nextcloud/
    #ServerName example.com
    #ServerAlias www.example.com
  

    Alias /nextcloud "/var/www/nextcloud/"

    <Directory /var/www/nextcloud/>
       Options +FollowSymlinks
       AllowOverride All
       Require all granted
        <IfModule mod_dav.c>
        	Dav off
        </IfModule>
       SetEnv HOME /var/www/nextcloud
       SetEnv HTTP_HOME /var/www/nextcloud
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
EOF
```

Phân quyền 
```sh 
chmod 755 -R /var/www/nextcloud/
chown www-data. -R /var/www/nextcloud/
```

Backup lại default config page 
```sh 
mv /etc/apache2/sites-available/000-default.{conf,conf.bk}
```

Enable các mode hỗ trợ 
```sh 
sudo a2enmod rewrite
sudo a2enmod headers
sudo a2enmod env
sudo a2enmod dir
sudo a2enmod mime
```

Enable config NextCloud và Reload lại apache 
```sh 
sudo a2ensite nextcloud.conf
sudo systemctl reload apache2
```

## Truy cập và Cấu hình NextCloud 
```sh 
https://server_domain_or_IP
```

Kết nối tài khoản MySQL đã tạo phía trên 
<p align="center">
<img src="/images/img-nextcloud/nextcloud-001.png">
</p>

Qúa trình tạo tài khoản đang được thực hiện, nếu có lỗi vui lòng kiểm tra lại tài khoản MySQL 
<p align="center">
<img src="/images/img-nextcloud/nextcloud-002.png">
</p>

Giao diện đăng nhập 
<p align="center">
<img src="/images/img-nextcloud/nextcloud-003.png">
</p>

Giao diện người dùng
<p align="center">
<img src="/images/img-nextcloud/nextcloud-004.png">
</p>


## Download Client 

NextCloud hỗ trợ gần như đầy đủ các Client để kết nối vào Server download <a href="https://nextcloud.com/install/#install-clients" target="_blank">tại đây</a>

Desktop 
<p align="center">
<img src="/images/img-nextcloud/nextcloud-005.png">
</p>

Mobile
<p align="center">
<img src="/images/img-nextcloud/nextcloud-006.png">
</p>
---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
