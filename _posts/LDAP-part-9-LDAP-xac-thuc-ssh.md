---
title: LDAP part 8 - Tích hợp LDAP với Graylog
categories:
  - LDAP
description: Tìm hiểu về LDAP.
author: datpt
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 22
---

## Lời mở đầu.

Hôm nay mình sẽ hướng dẫn mọi người cách để tích hợp LDAP với các máy chủ CentOS 7 và Ubuntu.

## Chuẩn bị.

- Máy ảo đã được cài đặt CentOS hoặc Ubuntu.

- Một máy CentOS 7 đã được [cài đặt OpenLDAP]()

## Thao tác trên OpenLDAP.

Tạo cây thư mục user như sau:

![ldap-32](/images/img-ldap-datpt/ldap-32.png)

## Thao tác trên máy có hệ điều hành CentOS 7.

Cài đặt OpenLDAP client:

```sh
yum -y install openldap-clients nss-pam-ldapd
```

Cấu hình lại file `/etc/nsswitch.conf` từ dòng 33 đến 35 như sau :

```sh
passwd:     ldap compat
shadow:     ldap compat
group:      ldap compat
```

Thêm đoạn cấu hình session như sau :

```sh
cat > /etc/pam.d/common-session <<EOF
session required    pam_mkhomedir.so skel=/etc/skel umask=0022
EOF
```

Khởi động `nscd`:

```sh
systemctl start nscd
systemctl enable nscd
```

Thực update config từ LDAP server :

```sh
authconfig --enableldap \
--enableldapauth \
--ldapserver=10.10.10.187 \
--ldapbasedn="dc=nhanhoa,dc=local" \
--enablemkhomedir \
--update
```

## Thao tác trên máy cài đặt Ubuntu.

Cài đặt `libpam-ldap` và `nscd`:

```sh
apt-get install libpam-ldap nscd
```

Cấu hình lại file `/etc/nsswitch.conf` từ dòng 33 đến 35 như sau :

```sh
passwd:     ldap compat
shadow:     ldap compat
group:      ldap compat
```

Chúc mọi người thành công !

Thêm đoạn cấu hình session như sau :

```sh
cat > /etc/pam.d/common-session <<EOF
session required    pam_mkhomedir.so skel=/etc/skel umask=0022
EOF
```

Khởi động lại `nscd`:

```sh
systemctl start nscd
systemctl enable nscd
```

## Tiến hành ssh bằng user trên LDAP.

Kết quả như sau:

![ldap-62](/images/img-ldap-datpt/ldap-62.png)

Chúc các bạn thành công !

## Tổng kết.

Cảm ơn mọi người đã đến với Cloud365, để chất lượng những bài viết được tốt hơn rất mong có những đóng góp từ mọi người.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>