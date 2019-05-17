---
title: "LDAP [Part 14] - Hướng dẫn backup và restore trên CentOS 7"
categories:
  - LDAP
description: Hướng dẫn backup và restore trên CentOS 7
author: thanhnb
tags: [LDAP, Linux, CentOS7]
type: Document
set: tim-hieu-LDAP
set_order: 14
---


## Chuẩn bị

```
HOSTNAME    ldap_94 (Main)
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: Cloud365a@123 (10.10.10.94)

HOSTNAME    ldap_96 (Backup)
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: Cloud365a@123 (10.10.10.96)
```

## Thực hiện Backup và Restore

### Tại node LDAP 94

Thực hiện dump data hay backup dữ liệu LDAP
```
ldapsearch -x -h 10.10.10.94 -b "dc=nhanhoa,dc=local" -D "cn=Manager,dc=nhanhoa,dc=local" -w Cloud365a@123 -s sub "(objectclass=*)" > backup_file.ldif
```

Lưu ý:
- `-b`: Base search
- `-h`: Chỉ định IP của host LDAP (10.10.10.94)
- `-x`: Sử dụng simple bind
- `-D`: binddn - Tài khoản đăng nhập vào LDAP
- `-w`: Mật khẩu (nhập tay)
- `-s`: Search level hay scope (base, one, sub)

Kết quả
```
[root@ldap_94 ~]# ll
-rw-r--r--  1 root root 12281 21:08 10 Th09 backup_file.ldif
```

Thực hiện chuyển file backup từ node 94 sang node 96
```
scp backup_file root@10.10.10.96:/root/
```

### Tại node LDAP 96

Lưu ý:
- Node LDAP 96 phải mới tinh, chỉ cài đặt xong LDAP

Thực hiện Restore dữ liệu 

```
ldapmodify -a -c -x -h 10.10.10.96 -D "cn=Manager,dc=nhanhoa,dc=local" -w Cloud365a@123 -f backup_file.ldif
```

Lưu ý:
- `-a`: Thêm mới đối tượng nếu không có đối tượng trong LDAP
- `-c`: Chế độ đọc liên tục, tức load hết các file vào LDAP kể cả có trường hợp lỗi
- `-x`: Sử dụng simple bind
- `-h`: Chỉ định IP của host LDAP (10.10.10.94)
- `-D`: binddn - Tài khoản đăng nhập vào LDAP
- `-w`: Mật khẩu (nhập tay)
- `-f`: File load

Kết quả
```
adding new entry "dc=nhanhoa,dc=local"
ldap_add: Already exists (68)

adding new entry "cn=Manager,dc=nhanhoa,dc=local"
ldap_add: Already exists (68)

adding new entry "ou=People,dc=nhanhoa,dc=local"
ldap_add: Already exists (68)

adding new entry "ou=Group,dc=nhanhoa,dc=local"
ldap_add: Already exists (68)

adding new entry "ou=Gitlab,dc=nhanhoa,dc=local"

adding new entry "cn=cody_castillo,ou=Gitlab,dc=nhanhoa,dc=local"

......

```

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>