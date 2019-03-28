---
title: Cài đặt OwnCloud CentOS 7 
categories:
  - Linux
description: Cài đặt OwnCloud CentOS 7 
author: canhdx
tags: [Beginer, Linux,  Other]
type: Document
---

## OwnCloud 

ownCloud là một phần mềm mã nguồn mở được sử dụng cho các dịch vụ lưu trữ tệp. Về mặt chức năng của ownCloud có những điểm tương đồng với Dropbox.

## Chuẩn bị môi trường cài đặt 

1 Server
- OS: CentOS7
- RAM: >= 1 GB
- Disk: >= 10 GB
- CPU: >= 1 Core
- Yêu cầu có kết nối internet để cài đặt
- Update và cài đặt openssh-server
```sh 
yum install -y epel-release
yum update -y 
```

<p align="center">
<img src="/images/img-owncloud/owncloud.png">
</p>

## Firewalld & SElinux
Mở firewalld cho traffic của httpd (web) đi qua 
```sh 
firewall-cmd --permanent --zone=public --add-service=http 
firewall-cmd --permanent --zone=public --add-service=https
firewall-cmd --reload
```

Cấu hình disable SElinux
```sh 
sudo setenforce 0
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=permissive/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
sed -i 's/SELINUX=permissive/SELINUX=disabled/g' /etc/selinux/config
```

## Cài đặt httpd 
```sh 
sudo yum -y install httpd 
```

Khởi dộng httpd 
```sh 
sudo systemctl start httpd.service
```

Khởi động httpd cùng hệ thống 
```sh 
sudo systemctl enable httpd.service
```

Kiểm tra ip hiện tại của Server 
```sh 
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

Truy cập 
```sh 
http://your_server_IP_address/
```

<p align="center">
<img src="/images/img-lamp/default_apache.png">
</p>

## Cài đặt MySQL(MariaDB)

Cài đặt
```sh 
sudo yum install -y mariadb-server
```

Start MariaDB
```sh 
sudo systemctl start mariadb
```

Cài đặt mật khẩu `root` cho MariaDB 
```sh 
sudo mysql_secure_installation
```
> Mặc đinh mật khẩu root của mysql sẽ để trống. Bước này có thể có hoặc không có tùy nhu cầu của Administrators

Khởi động MariaDB
```sh 
sudo systemctl enable mariadb.service
```

Tạo DB và user 
```sh 
mysql
CREATE DATABASE owncloud;
CREATE USER 'oc_user'@'localhost' IDENTIFIED BY 'YOUR_PASSWORD_HERE';
GRANT ALL PRIVILEGES ON owncloud.* TO 'oc_user'@'localhost';
FLUSH PRIVILEGES;
exit
```

## Cài đặt PHP (Ở đây chúng ta sử dụng phiên bản php7.2)
```sh 
yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm
yum install -y yum-utils
yum-config-manager --enable remi-php72
yum install php php-mysql php-dom php-mbstring php-gd php-pdo php-json php-xml php-zip php-curl php-mcrypt php-pear php-intl setroubleshoot-server -y 
```

## Cài đặt OwnCloud 

Phiên bản hiện tại 	10.1.0 	2019/02/07

import OwnCloud key 
```sh 
sudo rpm --import https://download.owncloud.org/download/repositories/stable/CentOS_7/repodata/repomd.xml.key
```

Download 
```sh 
sudo curl -L https://download.owncloud.org/download/repositories/stable/CentOS_7/ce:stable.repo -o /etc/yum.repos.d/ownCloud.repo
```

Clean repo 
```sh 
sudo yum clean expire-cache
```

Cài đặt OwnCloud 
```sh 
sudo yum install -y owncloud
```

Khởi tạo DB cho OwnCloud
```sh 
mysql -u root -p
```

```sh 
CREATE DATABASE owncloud;
GRANT ALL ON owncloud.* to 'owncloud'@'localhost' IDENTIFIED BY 'set_database_password';
FLUSH PRIVILEGES;
exit
```

## Truy cập và Cấu hình OwnCloud 
```sh 
https://server_domain_or_IP/owncloud
```

Tạo User và password cho tài khoản, Kết nối tài khoản MySQL đã tạo phía trên 
<p align="center">
<img src="/images/img-owncloud/owncloud-01.png">
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