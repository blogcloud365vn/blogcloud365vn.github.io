---
date: 2019-05-05
title: Hướng dẫn đăng nhập zabbix khi quên mật khẩu Admin
categories:
  - Monitor
description: Hướng dẫn đăng nhập zabbix khi quên mật khẩu Admin
author: niemdt
tags: [Zabbix, mysql]
type: Document
---

Có nhiều lần tôi quên mất password của user admin trong khi login vào zabbix server. Những lần như vậy mất khá nhiều thời gian để nhớ lại password của nó. Trong bài viết này tôi sẽ chỉ ra một cách giải quyết vấn đề này một cách khá nhanh mà tôi hay dùng.

![](/images/img-quen-password-zabbix/1.png)

Vì tất cả dữ liệu của zabbix được lưu trữ trong database và user đăng nhập zabbix cũng vậy nó cũng được lưu trong database. Database sử dụng cho zabbix ở đây tôi sử dụng là mysql. Như vậy khi bạn quên mật khẩu đăng nhập zabbix bạn có thể đổi mật khẩu trực tiếp trong DB.

Trước tiên login mysql với user có quyền với DB của zabbix. Ở đây tôi sử dụng user `root` để login mysql.

```
mysql -u root -p
```

Tiếp theo bạn có thể kiểm tra các user trong zabbix. Thực hiên lần lượt các lệnh sau:

```
mysql> use zabbix;
mysql>  select userid,alias,passwd from users;
```

![](/images/img-quen-password-zabbix/2.png)

Ta thấy password ở đây đã được mã hóa bằng hàm MD5. Như vậy muốn đổi password thì ta cũng phải thực hiện mã hóa password của ta bằng hàm MD5. Để mã hóa ta thực hiện như sau

```
niemdt@niemdt:~$ echo -n pass123 | openssl md5
(stdin)= 32250170a0dca92d53ec9624f336ca24
```

Như ví dụ trên password của tôi là `pass123` bạn thay thế passwd của bạn vào vị trí đó.

Tiếp theo tiếp hành đổi password mới cho user `Admin`

```
mysql> update users set passwd='32250170a0dca92d53ec9624f336ca24' where alias='Admin';
Query OK, 1 row affected (0,00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

Password ở đây là chuỗi các ký tự mã hóa cho password ở phía trên.

Bây giờ bạn có thể login zabbix server với password bạn vừa tạo.

Tôi thấy đây là một cách giải quyết khá dễ dàng trong trường hợp ta quên password để login zabbix server. 

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>