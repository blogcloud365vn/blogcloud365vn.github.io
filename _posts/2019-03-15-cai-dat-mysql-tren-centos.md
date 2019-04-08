---
title: Cài đặt MySQL trên CentOS7
categories:
  - Linux
description: Cài đặt MySQL trên CentOS7
author: canhdx
tags: [Beginer, Linux, MySQL, CentOS, Other]
type: Document
---

## Giới thiệu cơ bản 

MySQL là một hệ thống quản trị cơ sở dữ liệu (CSDL) mã nguồn mở thường được cài đặt trong bộ LAMP(Linux, Apache, MySQL, PHP) hoặc LEMP(Linux, Nginx, MySQL, PHP). MySQL được xếp vào loại CSDL có cấu trúc Structured Query Language hay chúng ta thường gọi là SQL

<p align="center">
<img src="/images/img-mysql/mysql01.jpg">
</p>

## Chuẩn bị môi trường cài đặt 

1 Server Linux cài đặt MySQL server
- OS: CentOS
- RAM: >= 1 GB
- Disk: >= 10 GB
- CPU: >= 1 Core
- Update và cài đặt openssh-server
- IP: 10.10.10.103/24

1 máy client (Ubuntu,CentOS,Windows)
- IP: 10.10.10.7/24

Yêu cầu có kết nối internet để cài đặt 

<p align="center">
<img src="/images/img-mysql/mysql03.png">
</p>

## Cài đặt 

Đăng nhập vào node MySQL-Server 
```sh 
ssh root@10.10.10.103
```

Cài đặt epel-release và cập nhật OS
```sh 
yum install epel-release -y
yum update -y
```

Chuẩn bị repo để cài đặt MySQL

- CentOS7
```sh 
rpm -ivh http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm 
```

- CentOS6
```sh 
rpm -ivh http://dev.mysql.com/get/mysql57-community-release-el6-10.noarch.rpm
```

Cài đặt và khởi động MySQL

- CentOS7
```sh 
yum -y install mysql-community-server 
systemctl start mysqld 
systemctl enable mysqld 
```

- CentOS6
```sh 
yum -y install mysql-community-server 
service mysqld start
chkconfig mysqld on
```

Sau khi cài đặt mặc định MySQL sẽ tự động tạo ra 1 root password tạm thời 
```sh 
temp_password=$(grep password /var/log/mysqld.log | awk '{print $NF}' | head -n1)
echo $temp_password
```

Để đổi password chúng ta thực hiện lần lượt 

- Đặt password mới 
```sh 
mysql_passwd="NEW_PASSWORD"
```
> với `NEW_PASSWORD` là password mới của bạn

- Tạo file reset password cho MySQL
```sh 
echo "ALTER USER 'root'@'localhost' IDENTIFIED BY '$mysql_passwd'; flush privileges;" > reset_pass.sql
```

- Đối với 1 vài password chúng ta sẽ cho phép đăng nhập `bớt secure` (bớt khắt khe) đi một tý như sau (Đương nhiên bạn có thể bỏ qua bước này nếu mật khẩu bạn đủ mạnh)
```sh 
echo "validate_password_policy=LOW" >> /etc/my.cnf
systemctl restart mysqld || service mysqld restart
```

Tiến hành đổi mật khẩu `root` của MySQL
```sh 
mysql -u root --password=$temp_password --connect-expired-password < reset_pass.sql
```

Đăng nhập MySQL với password mới để kiểm tra 
```sh 
mysql -uroot -p
```

Kết quả thành công 

```sh 
[root@mysql-server ~]# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.25 MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

Kiểm tra user và quyền đăng nhập 
```sh 
mysql> SELECT user,host  FROM mysql.user WHERE user = 'root';
+------+-----------+
| user | host      |
+------+-----------+
| root | localhost |
+------+-----------+
1 row in set (0,00 sec)

mysql> 
```
> Ở đây user `root` chỉ có thể đăng nhập trên `localhost` (Chính trên MySQL-Server)

Để có thể kết nối từ bên ngoaì vào MySQL server cần cấp quyền cho user 
```sh 
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'NEW_PASWORD';
FLUSH PRIVILEGES;FLUSH PRIVILEGES;
```

Chỉnh sửa lại `/etc/my.cnf` 
```sh 
#bind-address = 127.0.0.1 
```
> Comment lại dòng này nếu có

Tiến hành restart lại service MySQL sau khi chỉnh sửa `/etc/my.cnf`
```sh 
systemctl restart mysqld || service mysqld restart
```

## Kết nối đến MySQL-Server

Kết nối đến MySQL đơn giản hơn bạn nghĩ rất nhiều có nhiều cách để kết nối đến MySQL và làm việc với nó. 

- Cách 1: Đăng nhập trực tiếp trên Console ssh 
- Cách 2: Sử dụng `phpMyadmin` <a href="https://cloud365.vn/" target="_blank">Hướng dẫn cài đặt phpmyadmin</a>
- Cách 3: Sử dụng Client như `MySQL Workbench`

Đối với Cách 1 và 2 chúng ta không cần phải chỉnh sửa quyền đăng nhập của user vì chúng thực hiện hoàn toàn trên localhost của MySQL server 

Đối với Cách 3 thì chúng ta phải chỉnh sửa lại quyền đăng nhập của user để có thể kết nối từ bên ngoài vào.

Cài đặt MySQL Workbench và mở lên 
<p align="center">
<img src="/images/img-mysql/mysql04.png">
</p>

Khai báo kết nối 
<p align="center">
<img src="/images/img-mysql/mysql05.png">
</p>

Giao diện kết nối thành công 
<p align="center">
<img src="/images/img-mysql/mysql07.png">
</p>

## Hoàn tất cài đặt kết nối
Chúng tôi hy vọng bài viết sẽ tiết kiệm được cho các bạn một chút thời gian khi bắt đầu làm việc với MySQL Server
---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>

