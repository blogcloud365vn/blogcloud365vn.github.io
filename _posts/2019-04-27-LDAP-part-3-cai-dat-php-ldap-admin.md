---
title: LDAP [Part 3] - Hướng dẫn cài đặt phpLDAP- [Cài đặt OpenLDAP](https://cloud365.vn/ldap/LDAP-part-2-cai-dat-ldap-centos-7/){:target="_blank"}
categories:
  - LDAP
description: Hướng dẫn cài đặt phpldapadmin trên CentOS 7
author: datpt
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 3
---

## Lời mở đầu.

Đối với hệ thống OpenLDAP thì việc quản lý, update tài khoản người dùng phải dùng bằng lệnh cho nên khá khó khăn do đó hôm này mình sẽ giới thiệu đến mọi người một công cụ giúp chúng ta có thể dễ dàng quản lý OpenLDAP đó là phpLDAPadmin.Một công cụ được code bằng ngôn ngữ PHP và chạy trên nền web cho nên ứng dụng này đáng được lựa chọn nếu như chúng ta đang cần một công cụ hỗ trợ việc quản trị OpenLDAP.

## Chuẩn bị.

- [Cài đặt OpenLDAP trên CentOS 7](https://cloud365.vn/ldap/LDAP-part-2-cai-dat-ldap-centos-7/){:target="_blank"}

## TRƯỜNG HỢP 1: Cài đặt trực tiếp trên node cài OpenLDAP

**1. Cài đặt httpd**

```sh
yum -y install httpd
```

Mở file `/etc/httpd/conf/httpd.conf` và sửa đổi các thông tin sau:

```sh
# Tại dòng 151 sửa như sau :
AllowOverride All

# Tại dòng 164, sửa thông tin như sau:
DirectoryIndex index.html index.cgi index.php

# Thêm vào cuối file những cấu hình sau:
ServerTokens Prod
KeepAlive On
```

Khởi động httpd:

```sh
systemctl start httpd
systemctl enable httpd
```

**2. Cài đặt PHP**

```sh
yum -y install php php-mbstring php-pear
```

Mở file `/etc/php.ini` và sửa lại các thông tin như sau:

```sh
# Tại dòng 878 sửa lại timezone:
date.timezone = "Asia/Ho_Chi_Minh"
```

Tải và cài dặt epel 7:

```sh
yum install wget -y
wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -ivh epel-release-latest-7.noarch.rpm
```

**Cài đặt phpLDAPadmin**

```sh
yum --enablerepo=epel -y install phpldapadmin
```

Mở file `/etc/phpldapadmin/config.php` và sửa lại thông tin như sau:

```sh
# Tại dòng 398:
$servers->setValue('login','attr','dn');
// $servers->setValue('login','attr','uid');
```

Cho phép truy cập vào phpLDAPadmin:

```sh
cat > /etc/httpd/conf.d/phpldapadmin.conf << EOF
Alias /phpldapadmin /usr/share/phpldapadmin/htdocs
Alias /ldapadmin /usr/share/phpldapadmin/htdocs

<Directory /usr/share/phpldapadmin/htdocs>
  <IfModule mod_authz_core.c>
    # Apache 2.4
    Require all granted
  </IfModule>
  <IfModule !mod_authz_core.c>
    # Apache 2.2
    Order Deny,Allow
    Deny from all
    Allow from 127.0.0.1
    Allow from ::1
  </IfModule>
</Directory>
EOF
```

Khởi động lại httpd:

```sh
systemctl restart httpd
```

Truy cập theo đường dẫn :

```sh
IP_PHP_LDAP_ADMIN/phpldapadmin
```

Kết quả như sau:

![ldap-10](/images/img-ldap-datpt/ldap-10.png)

Đăng nhập với `dn` và `password` như sau:

![ldap-11](/images/img-ldap-datpt/ldap-11.png)

Giao diện sau khi đăng nhập :

![ldap-12](/images/img-ldap-datpt/ldap-12.png)

## TRƯỜNG HỢP 2: phpLDAPadmin nằm trên node không cài OpenLDAP.

Mô hình như sau:

![ldap-13](/images/img-ldap-datpt/ldap-13.png)

Các bước cài đặt tương tự như trên, chúng ta cần phải thêm đoạn cấu hình để bind đến OpenLDAP server thì mới có thể đăng nhập được. Để thực hiện chúng ta dùng trình soạn thảo `vi` mở file `/usr/share/phpldapadmin/config/config.php`

```sh
vi /usr/share/phpldapadmin/config/config.php
```

Sau đó tại dòng `584` thêm đoạn `$servers->setValue('server','host','10.10.10.185');` với `10.10.10.185` là ip của máy chủ OpenLDAP.

![ldap-14](/images/img-ldap-datpt/ldap-14.png)

Sau đó truy cập vào web như bình thường.

## TRƯỜNG HỢP 3: Dùng một máy chủ phpLDAPadmin quản lý nhiều backend OpenLDAP

Giả định rằng chúng ta có khoảng 3 máy chủ chạy OpenLDAP để chạy xác thực cho các hệ thống khác nhau và 3 máy chủ này không hề có liên quan gì đến nhau, trên lý thuyết chúng ta phải cài 3 máy chủ chạy OpenLDAP để quản lý 3 máy OpenLDAP kia, tuy nhiên chúng ta vẫn có thể cấu hình phpLDAPadmin bind đến nhiều máy chủ chỉ định khác nhau. Ở đâu mình có mô hình như sau :

![ldap-15](/images/img-ldap-datpt/ldap-15.png)

Chúng ta vẫn cài phpLDAPadmin như ở trường hợp 1, sau đó chúng ta mở file `/usr/share/phpldapadmin/config/config.php` và thêm những cấu hình như sau:

```sh
# OpenLDAP1
$servers->setValue('server','host','10.10.10.185');

# OpenLDAP2
$servers->newServer('ldap_pla');
$servers->setValue('server','name','LDAP Server 2');
$servers->setValue('server','host','10.10.10.184');
$servers->setValue('server','port',389);
$servers->setValue('server','base',array(''));
$servers->setValue('login','auth_type','cookie');
$servers->setValue('login','bind_id','');
$servers->setValue('login','bind_pass','');
$servers->setValue('server','tls',false);

# OpenLDAP3
$servers->newServer('ldap_pla');
$servers->setValue('server','name','LDAP Server 3');
$servers->setValue('server','host','10.10.10.183');
$servers->setValue('server','port',389);
$servers->setValue('server','base',array(''));
$servers->setValue('login','auth_type','cookie');
$servers->setValue('login','bind_id','');
$servers->setValue('login','bind_pass','');
$servers->setValue('server','tls',false);
```

Chi tiết chỉnh sửa như sau :

![ldap-16](/images/img-ldap-datpt/ldap-16.png)

Nếu có thêm những server khác thì thêm đoạn cấu hình giống `OpenLDAP2` xuống dưới sau đó lưu lại.

Kiểm tra trên giao diện web, bây giờ chúng ta có thể chọn máy chủ quản trị trước khi đăng nhập rồi:

![ldap-17](/images/img-ldap-datpt/ldap-17.png)

Bài blog đến đây là hết, chúc mọi người thành công !!!



## Tổng kết.

Ở bài này mình đã hướng dẫn cách để  cài đặt phpLDAPadmin cho mọi người có thể dụng để quản trị các thông tin trong slapd, ở bài viết [tiếp theo](https://cloud365.vn/ldap/LDAP-part-4-Ldap-xac-thuc-ssh/){:target="_blank"} mình sẽ giới thiệu cho mọi người cách SSH với LDAP.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>