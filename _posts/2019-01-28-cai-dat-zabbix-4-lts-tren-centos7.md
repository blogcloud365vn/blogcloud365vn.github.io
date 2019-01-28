---
date: 2019-01-28
title: Hướng dẫn cài đặt Zabbix 4.0 LTS trên CentOS 7
categories:
  - Monitor
description: Tài liệu hướng dẫn các bước cài đặt Zabbix 4.0 LTS trên CentOS 7
author: duydm
tags: [Beginer, Zabbix]
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

** Lưu ý **: Bạn có thể tùy chỉnh cấu hình theo số lượng host bạn muốn giám sát. 

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

![](../images/img-install-zabbix4/Screenshot_347.png)

### Bước 2: Create Db

Start service mariadb và auto start khi khởi động lại server

```
systemctl start mariadb
systemctl enable mariadb
```
Thiết lập password root cho mysql

```
mysql_secure_installation
```

![](../images/img-install-zabbix4/Screenshot_348.png)

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

![](../images/img-install-zabbix4/Screenshot_349.png)














---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
