---
date: 2019-05-13
title: Hướng dẫn turning zabbix
categories:
  - Monitor
description: Hướng dẫn turning zabbix
author: niemdt
tags: [Zabbix, MySQL]
type: Document
---

Khi bạn sử dụng zabbix để giám sát một hệ thống lớn lúc này DB zabbix của bạn sẽ rất lớn. Thông thường khi bạn sử dụng MySQL để lưu DB trong zabbix thì DB này sẽ được lưu thành một file. Như vậy theo thời gian dung lượng của file này sẽ rất lớn. Ta có thể dễ dàng nhận ra rằng việc đọc dữ liệu ở một file lớn sẽ mất nhiều thời gian hơn việc đọc dữ liệu từ những file có dung lượng nhỏ hơn. Trong bài viết này tôi sẽ chỉ ra cách để chuyển DB được lưu tất cả trong một file thành nhiều file nhỏ mà mỗi table sẽ được lưu thành một file.

Chúng ta có thể chia nhỏ file có dung lượng lớn `ibdata1` thành những file nhỏ và như vậy chúng ra có thể giảm dung lượng ở những bảng có dữ liệu không cần nữa ví dụ như bảng `history-uint`

```
# ls -lh /var/lib/mysql
total 16G
-rw-rw---- 1 mysql mysql  16K Aug 15 10:36 aria_log.00000001
-rw-rw---- 1 mysql mysql   52 Aug 15 10:36 aria_log_control
-rw-rw---- 1 mysql mysql  15G Aug 15 12:45 ibdata1
-rw-rw---- 1 mysql mysql 5.0M Aug 15 12:45 ib_logfile0
-rw-rw---- 1 mysql mysql 5.0M Aug 15 12:45 ib_logfile1
drwx------ 2 mysql mysql 4.0K Aug 15 10:36 mysql
srwxrwxrwx 1 mysql mysql    0 Aug 15 10:44 mysql.sock
drwx------ 2 mysql mysql 4.0K Aug 15 10:36 performance_schema
drwx------ 2 mysql mysql    6 Aug 15 10:36 test
drwx------ 2 mysql mysql 8.0K Aug 15 13:59 zabbix
```

Ta có thể thấy file `ibdata1` có dung lượng khá lớn.

## Stop dịch vụ zabbix

Trước khi bắt đầu chúng ta cần stop `zabbix-server` 

```
systemctl stop zabbix-server
```

## Backup database

**1** Snapshot: Nếu zabbix server được cài trên máy ảo thì chúng ta nên tạo nên một bản snapshot để khi ta có một thao tác nào nhầm lẫn thì bạn vẫn có thể revert lại bản snapshot vừa rồi.

**2** Dump DB

Để dữ lại toàn bộ dữ liệu tại thời điểm hiện tại ta dùng `mysqldump` để dump toàn bộ DB lại thành một file.

```
mysqldump -u zabbix -p --all-databases --add-drop-table > zabbix_db.sql

mkdir backup

mv zabbix_db.sql backup
```

Ta thấy trong thư mục `backup` đã có file ta vừa dump

```
[root@grafana backup]# ls
zabbix_db.sql
```

**3** Backup thư mục /var/lib/mysql

```
rsync -avP /var/lib/mysql backup
```

## Cho phép lưu mỗi bảng một file

1. Stop mysql

```
systemctl stop mysql
```

2. Backup thư mục `/etc/my.cnf`

```
cp /etc/my.cnf /etc/my.cnf.20190509
```

3. Thêm dòng `innodb_file_per_table=1` bên dưới `[mysqld]` trong file `/etc/my.cnf`

```
[root@grafana ~]# vi /etc/my.cnf

[mysqld]

...

innodb_file_per_table=1
```

## Build lại database

Remove tất cả các file trong thư mục `/var/lib/mysql`

```
rm -rf /var/lib/mysql/*
```

Cần lưu ý rằng bước này sẽ xóa toàn bộ database nên bạn cần chắc chắn rằng bạn đã thực hiện các bước backup trước đó.

Khởi tạo lại database

```
/usr/bin/mysql_install_db
```

Kiểm tra lại thư mục `/var/lib/mysql` sau khi khởi tạo lại database

```
[root@grafana ~]# ls -lh /var/lib/mysql

total 28K
-rw-rw---- 1 root root  16K Aug 15 10:36 aria_log.00000001
-rw-rw---- 1 root root   52 Aug 15 10:36 aria_log_control
drwx------ 2 root root 4.0K Aug 15 10:36 mysql
drwx------ 2 root root 4.0K Aug 15 10:36 performance_schema
drwx------ 2 root root    6 Aug 15 10:36 test
```

Thau đổi quyền cho tất cả các file 

```
chown -R mysql:mysql /var/lib/mysql/
```

Bây giờ thực hiện kiểm tra lại

```
[root@grafana ~]# ls -lh /var/lib/mysql

total 28700
-rw-rw---- 1 mysql mysql    16384 Aug 15 10:36 aria_log.00000001
-rw-rw---- 1 mysql mysql       52 Aug 15 10:36 aria_log_control
-rw-rw---- 1 mysql mysql 18874368 Aug 15 10:41 ibdata1
-rw-rw---- 1 mysql mysql  5242880 Aug 15 10:41 ib_logfile0
-rw-rw---- 1 mysql mysql  5242880 Aug 15 10:40 ib_logfile1
drwx------ 2 mysql mysql     4096 Aug 15 10:36 mysql
drwx------ 2 mysql mysql     4096 Aug 15 10:36 performance_schema
drwx------ 2 mysql mysql        6 Aug 15 10:36 test
```

Start dịch vụ MySQL

```
systemctl start mysql
```

## Rebuild the Account

Login vaò MySQL với user `root` và switch sang database `mysql`

```
[root@grafana ~]# mysql -u root -p
Enter password: 
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
[root@grafana ~]# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.6.44-log MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> 
```

Thay đổi password của user root

```
mysql> update user set Password=password("admin") where User="root";
Query OK, 4 rows affected (0,00 sec)
Rows matched: 4  Changed: 4  Warnings: 0
```

Tạo database sử dụng cho Zabbix

```
mysql> CREATE DATABASE zabbix;
Query OK, 1 row affected (0.00 sec)
```

Tạo account sử dụng cho Zabbix

```
mysql> CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.00 sec)
```

Gán quyền cho account Zabbix

```
mysql> GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```

Apply những gì vừa thiết lập

```
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)
```

## Kiểm tra lại file_per_table đã được setting

Trước khi import data đã backup trước đó chúng ta cần kiểm tra lại giá trị `innodb_file_per_table` đang có giá trị là `ON`

```
mysql> show variables like '%per_table%';
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| innodb_file_per_table | ON    |
+-----------------------+-------+
1 row in set (0,00 sec)
```

## Import lại file backup mà ta đã tiến hành backup trước đó

```
[root@grafana ~]# cd backup/
[root@grafana backup]# time mysql -u zabbix -p zabbix < zabbix_db.sql
Enter password:

real    31m57.704s
user    0m50.054s
sys     0m1.945s
```

Việc import có thể nhanh hay chậm tùy thuộc vào kích thước database của bạn.

## Xóa dữ liệu ở những bảng ko cần thiết

Một số bảng có giá trị mà ta không cần đến nó nữa. Ta có thế xóa nó đi để giảm bớt dung lượng

Ta có thể thấy những bảng có kích thước lớn.

```
# ls -lhtrS /var/lib/mysql/zabbix | tail
...
                                                            
-rw-rw---- 1 mysql mysql 192M Aug 15 14:01 events.ibd
-rw-rw---- 1 mysql mysql 288M Aug 15 14:01 trends.ibd
-rw-rw---- 1 mysql mysql 660M Aug 15 14:01 trends_uint.ibd
-rw-rw---- 1 mysql mysql 1.1G Aug 15 14:01 history.ibd
-rw-rw---- 1 mysql mysql  14G Aug 15 13:53 history_uint.ibd
```

Ta có thể thấy bảng `history_uint.ibd` có kích thước rất lớn. Bảng này lưu trữ tất cả các metric đẩy về từ các zabbix agent. Nên khi không có nhu cầu ta có thể xóa nó để lấy những metric mới hơn. Để làm việc này ta thực hiện như sau:

Login vào MySQL với user là `zabbix` và switch tới database `zabbix`

```
[root@grafana ~]# mysql -u zabbix -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 26
Server version: 5.6.44-log MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use zabbix;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> 
```

Đếm số record trong bảng `history_uint`

```
mysql> select count(itemid) from history_uint;
+---------------+
| count(itemid) |
+---------------+
|     177487167 |
+---------------+
1 row in set (1 min 14.37 sec)
```

Xóa các record trong bảng `history_uint`

```
mysql> truncate table history_uint;
Query OK, 0 rows affected (0.38 sec)
```

Xem lại số bản ghi trong bảng `history_unit`

```
mysql> select count(itemid) from history_uint;
+---------------+
| count(itemid) |
+---------------+
|           107 |
+---------------+
1 row in set (0.00 sec)
```

Ta có thể xem lại các bảng có kích thước lớn. Lúc này không thấy bảng `history_unit`

```
# ls -lhtrS /var/lib/mysql/zabbix | tail
...
                                                           
-rw-rw---- 1 mysql mysql 192M Aug 15 14:01 events.ibd
-rw-rw---- 1 mysql mysql 288M Aug 15 14:01 trends.ibd
-rw-rw---- 1 mysql mysql 660M Aug 15 14:01 trends_uint.ibd
-rw-rw---- 1 mysql mysql 1.1G Aug 15 14:01 history.ibd
```

**Tài liệu tham khảo**

https://medium.com/@chusiang/change-the-mysql-innodb-tablespaces-to-file-per-table-for-zabbix-3-2-6-on-centos-7-dd86ab3179a4

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>