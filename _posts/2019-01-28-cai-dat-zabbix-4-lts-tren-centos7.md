---
date: 2019-01-28
title: Hướng dẫn cài đặt Zabbix 4.0 LTS trên CentOS 7 (Phần 1)
categories:
  - Monitor
description: Tài liệu hướng dẫn các bước cài đặt Zabbix 4.0 LTS trên CentOS 7
author: duydm
tags: [Zabbix]
type: Document
---

### Mục lục

## 1. Yêu cầu cài đặt

+ Cầu hình Zabbix server

Ram: 8 GB<br>
CPU: 4 core<br>
Disk: 300GB<br>
Interface: 1<br>
OS : CentOS 7.6<br>

![](/images/img-zabbix-4lts/Screenshot_901.png)

**Lưu ý**: Bạn có thể tùy chỉnh cấu hình theo số lượng host bạn muốn giám sát. 

+ Mô hình triển khai

![](/images/img-zabbix-4lts/topo-zabbix.png)

+ Thiết lập ban đầu

Set hostname<br>

```
hostnamectl set-hostname <host-name>
```

Set địa chỉ IP tĩnh<br>

```
vi /etc/sysconfig/network-scripts/ifcfg-<interface>
```

![](/images/img-zabbix-4lts/Screenshot_903.png)

Update các package<br>

```
yum update -yum
```

Kiểm tra firewall, selinux<br>

![](/images/img-zabbix-4lts/Screenshot_902.png)

## 2. Các bước cài đặt

### Bước 1: Download repo zabbix và cài đặt một số package

```
yum install epel-release
rpm -ivh https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
yum -y install zabbix-server-mysql zabbix-web-mysql mysql mariadb-server httpd php
```

![](../images/img-zabbix-4lts/Screenshot_347.png)

### Bước 2: Create Database

Start service `mariadb` và tự động start khi khởi động lại server.

```
systemctl start mariadb
systemctl enable mariadb
```
Thiết lập password root cho mysql

```
mysql_secure_installation
```

![](../images/img-zabbix-4lts/Screenshot_348.png)

**Thay vì phải mở thủ công file config để chỉnh sửa ở đây tôi hướng dẫn bạn khai báo các biến để chỉnh sửa config được nhanh hơn.**

Khai báo biến và gán giá trị cho việc config database mysql

```
userMysql="root"
passMysql="passWord"
portMysql="3306"
hostMysql="localhost"
nameDbZabbix="zabbix_db"
userDbZabbix="zabbix_user"
passDbZabbix="passWord"
```
Config database zabbix

```
cat << EOF |mysql -u$userMysql -p$passMysql
DROP DATABASE IF EXISTS zabbix_db;
create database zabbix_db character set utf8 collate utf8_bin;
grant all privileges on zabbix_db.* to zabbix_user@localhost identified by 'passWord';
flush privileges;
exit
EOF
```

![](../images/img-zabbix-4lts/Screenshot_349.png)

### Bước 3: Import database zabbix

```
cd /usr/share/doc/zabbix-server-mysql-4.0.3
gunzip create.sql.gz
mysql -u root -p zabbix_db < create.sql
```

![](../images/img-zabbix-4lts/Screenshot_352.png)


### Bước 4: Config DB

```
sed -i 's/# DBHost=localhost/DBHost=localhost/g' /etc/zabbix/zabbix_server.conf
sed -i "s/DBName=zabbix/DBName=$nameDbZabbix/g" /etc/zabbix/zabbix_server.conf
sed -i "s/DBUser=zabbix/DBUser=$userDbZabbix/g" /etc/zabbix/zabbix_server.conf
sed -i "s/# DBPassword=/DBPassword=$passDbZabbix/g" /etc/zabbix/zabbix_server.conf
```

### Bước 5: Configure PHP Setting

```
sed -i 's/max_execution_time = 30/max_execution_time = 600/g' /etc/php.ini
sed -i 's/max_input_time = 60/max_input_time = 600/g' /etc/php.ini
sed -i 's/memory_limit = 128M/memory_limit = 256M/g' /etc/php.ini
sed -i 's/post_max_size = 8M/post_max_size = 32M/g' /etc/php.ini
sed -i 's/upload_max_filesize = 2M/upload_max_filesize = 16M/g' /etc/php.ini
echo "date.timezone = Asia/Ho_Chi_Minh" >> /etc/php.ini
```

### Bước 7: Restart service `zabbix-server`, `http`, `mariadb`

```
systemctl start zabbix-server
systemctl enable zabbix-server
systemctl start httpd
systemctl enable httpd
systemctl restart zabbix-server
systemctl restart httpd
systemctl restart mariadb
```
![](../images/img-zabbix-4ltsScreenshot_904.png)

### Bước 8: Truy cập web dashboard zabbix

http://ipserver/zabbix

![](../images/img-zabbix-4lts/Screenshot_353.png)

Các thiết lập ban đầu trên web dashboard zabbix mời các bạn thực hiện theo hướng dẫn ở phần 2.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
