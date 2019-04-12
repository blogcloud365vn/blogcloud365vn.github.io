---
title: Cài đặt NextCloud trên CentOS 7 
categories:
  - Linux
description: Cài đặt NextCloud trên CentOS 7 
author: canhdx
tags: [Beginer, Linux, CentOS, Other]
type: Document
---

## NextCloud 

<p align="center">
<img src="/images/img-nextcloud/nextcloud-logo.jpeg">
</p>

Ở bài viết trước chúng ta đã biết được về ownCloud là một phần mềm mã nguồn mở được sử dụng cho các dịch vụ lưu trữ tệp. Vậy còn NextCloud?

NextCloud là một nhánh được fork ra và phát triển riêng trên từ OwnCloud, nó được định hướng phát triển bởi một số thành viên cũ của OwnCloud. Trong khi Owncloud chậm cập nhật các tính năng thì NextCloud tập trung vào release và bổ sung các tính năng mà người dùng feedback. 

NextCloud cũng giống như OwnCloud hỗ trợ trên hầu như tất cả các Client hiện có 
<p align="center">
<img src="/images/img-nextcloud/nextcloud-000.png">
</p>

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

Khởi động httpd 
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
CREATE DATABASE nextcloud;
CREATE USER 'nc_user'@'localhost' IDENTIFIED BY 'YOUR_PASSWORD_HERE';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nc_user'@'localhost';
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

## Cài đặt NextCloud 

Phiên bản hiện tại 15.0.7	
Download 
```sh 
yum install wget -y 
wget https://download.nextcloud.com/server/releases/nextcloud-15.0.7.zip -O /opt/nextcloud.zip
```

Giải nén 
```sh 
yum install unzip -y 
unzip /opt/nextcloud.zip -d /var/www/html/
rm -f /opt/nextcloud.zip
```

Phân quyền 
```sh 
chmod 755 -R /var/www/html/
chown apache. -R /var/www/html/
```

Reload lại httpd
```sh 
user
```

## Truy cập và Cấu hình NextCloud 
```sh 
https://server_domain_or_IP/owncloud
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

## Tổng kết 
Bây giờ bạn đã có một Cloud riêng để lưu trữ dữ liệu. Việc tạo thêm các tài khoản, phân chia tài nguyên kết nối, kết nối thêm các backend lưu trữ sẽ được cập nhật trong các bài tiếp theo.
---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
