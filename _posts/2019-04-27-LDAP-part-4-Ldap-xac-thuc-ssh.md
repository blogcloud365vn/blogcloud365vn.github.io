---
title: LDAP [Part 4] - Xác thực SSH với LDAP
categories:
  - LDAP
description: Xác thực SSH với LDAP
author: datpt
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 4
---

## Lời mở đầu.

Sau khi cài đặt OpenLDAP và phpLDAPadmin, thì bây giờ là thời điểm mình đã có thể kiểm tra lại hoạt động của LDAP.

Để thực hiện kiểm tra, ứng dụng đầu tiên và đơn giản nhất mình sử dụng đó là SSH.

## Chuẩn bị.

- Máy ảo đã được cài đặt CentOS hoặc Ubuntu.

- Một máy CentOS 7 đã được [cài đặt OpenLDAP](https://cloud365.vn/ldap/LDAP-part-2-cai-dat-ldap-centos-7/){:target="_blank"}

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

## Tiến hành SSH bằng user trên LDAP.

Kết quả như sau:

![ldap-62](/images/img-ldap-datpt/ldap-62.png)

Chúc các bạn thành công !

## Tổng kết.

Như vậy trong bài viết này mình đã hướng dẫn các bạn cách SSH đến server CentOS và Ubuntu với tài khoản từ LDAP, trong bài viết tiếp theo mình sẽ hướng dẫn [tích hợp LDAP với Pfsense](https://cloud365.vn/ldap/LDAP-part-5-Tich-hop-ldap-voi-pfsense/){:target="_blank"}, một firewall mềm được rất nhiều đơn vị triển khai.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>