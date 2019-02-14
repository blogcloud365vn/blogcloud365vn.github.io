---
title: Cách để enable slow query log trong Mysql 5.6
categories:
  - Logging
description: Mysql slow queries log
author: datpt
tags: [Logging, Linux, Mysql]
type: Document
---

## Lời mở đầu.

- Trong quá trình sử dụng và vận hành hệ thống Mysql thì việc quản lý các câu truy vấn thực thi chậm trên hệ thống sẽ giúp chúng ta có thể giúp ích chúng ta cải thiện cũng như tra vết xem vấn đề nằm trong code của chúng ta hay do hệ thống chưa được tgoosi ưu tốt, hôm nay mình xin gửi đến mọi người hướng dẫn cấu hình log slow queries trong Mysql bản 5.6.

## Cấu hình bên trong Mysql không cần restart dịch vụ.

Login vào mysql:

```sh
mysql -u root -p
```

Enable slow queries log:

```sh
SET GLOBAL slow_query_log = 'ON';
```

Setup thời gian quy định slow query:

```sh
SET GLOBAL long_query_time = 5;
```

Kiểm tra path của log file :

```sh
SHOW VARIABLES LIKE 'slow_query_log_file';
```

Kết quả :

```sh
+---------------------+-----------------------------------+
| Variable_name       | Value                             |
+---------------------+-----------------------------------+
| slow_query_log_file | /var/lib/mysql/localhost-slow.log |
+---------------------+-----------------------------------+
1 row in set (0,00 sec)

```

Thay đổi log file slow queries:

```sh
SET GLOBAL slow_query_log_file = '/path/filename';
```

Thực hiện bài test kiểm tra:

```sh
SELECT SLEEP(10);
```

Kiểm tra file lưu trữ hoặc dùng lệnh sau để kiểm tra:

```sh
mysqladmin proc stat
```

Kết quả :

```sh
+----+------+-----------+----+---------+------+-------+------------------+
| Id | User | Host      | db | Command | Time | State | Info             |
+----+------+-----------+----+---------+------+-------+------------------+
| 4  | root | localhost |    | Query   | 0    | init  | show processlist |
+----+------+-----------+----+---------+------+-------+------------------+
Uptime: 416  Threads: 1  Questions: 16  Slow queries: 1  Opens: 68  Flush tables: 1  Open tables: 61  Queries per second avg: 0.038

```

## Cấu hình sửa trong file cấu hình, cần restart dịch vụ.

Mở file cấu hình :

```sh
vi /etc/my.cnf
```

Thêm đoạn cấu hình sau:

```sh
[mysqld]

slow_query_log                  = 1
slow_query_log_file             = /var/log/mysql/slow.log
long_query_time                 = 5
```

Restart mysqld:

```sh
systemctl restart mysqld
```

Test bằng một số câu lệnh ở phần trên.

## Tổng kết.

Như thế là chúng ta đã có thể theo dõi slow queries hệ thống mysql rồi. Chúc mọi người thành công !

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>