---
title: LDAP part 2 - Cài đặt OpenLDAP trên CentOS 7
categories:
  - LDAP
description: Tìm hiểu về LDAP.
author: datpt
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 29
---

## Lời mở đầu.

Ở bài trước mình đã giới thiệu với mọi người về xác thực tập chung và LDAP, ở bài này mình sẽ hướng dẫn mọi người cài đặt LDAP trên CentOS 7.

## Chuẩn bị.

- Một máy đã được cài sẵn OS: CentOS 7.

- Cấu hình đề nghị : RAM 2GB/ 2vCPUs/ 20 GB SSD.

- Tất cả đều được thực hiện dưới quyền root.

## Các bước chuẩn bị trước khi cài đặt.

Cấu hình hostname:

```sh
hostnamectl set-hostname "LDAP"
excec bash
```

Tắt firewalld :

```sh
systemctl stop firewalld
systemctl disable firewalld
```

Tắt Selinux:

```sh
sudo setenforce 0
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=permissive/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
sed -i 's/SELINUX=permissive/SELINUX=disabled/g' /etc/selinux/config
```

Khởi động lại máy để lấy lại cấu hình mới nhất :

```sh
init 6
```

Cài đặt `epel-release` và cập nhật các gói phần mềm :

```sh
yum install epel-release -y
yum update -y
```

## Cài đặt OpenLDAP

Cài đặt `openldap-servers` và `openldap-clients`:

```sh
yum -y install openldap-servers openldap-clients
```

Sao chép file cấu hình và phân quyền :

```sh
cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG
chown ldap. /var/lib/ldap/DB_CONFIG 
```

Khởi động slapd:

```sh
systemctl start slapd
systemctl enable slapd
```

Thiết lập LDAP admin password, tạo mật khẩu :

```sh
slappasswd 
New password:
Re-enter new password:
{SSHA}adminpasswd
```

Thêm mới file `chroot.ldif`:

```sh
cat > chrootpw.ldif << EOF
dn: olcDatabase={0}config,cn=config
changetype: modify
add: olcRootPW
olcRootPW: {SSHA}adminpasswd
EOF
```

Chạy lệnh sau để  update thông từ file `chroot.ldif`:

```sh
ldapadd -Y EXTERNAL -H ldapi:/// -f chrootpw.ldif
```

Import các schemas:

```sh
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
```

Thiết lập Manager Password, tạo mật khẩu :

```sh
slappasswd 
New password:
Re-enter new password:
{SSHA}managerpassword
```

Thêm mới file `chdomain.ldif`:

```sh
cat > chdomain.ldif << EOF
dn: olcDatabase={1}monitor,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth"
  read by dn.base="cn=Manager,dc=nhanhoa,dc=local" read by * none

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=nhanhoa,dc=local

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=Manager,dc=nhanhoa,dc=local

dn: olcDatabase={2}hdb,cn=config
changetype: modify
add: olcRootPW
olcRootPW: {SSHA}managerpassword

dn: olcDatabase={2}hdb,cn=config
changetype: modify
add: olcAccess
olcAccess: {0}to attrs=userPassword,shadowLastChange by
  dn="cn=Manager,dc=nhanhoa,dc=local" write by anonymous auth by self write by * none
olcAccess: {1}to dn.base="" by * read
olcAccess: {2}to * by dn="cn=Manager,dc=nhanhoa,dc=local" write by * read
EOF
```

Chạy lệnh sau để update thông tin:

```sh
ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif 
```

Thêm file `basedomain.ldif`:

```sh
cat > basedomain.ldif << EOF
dn: dc=nhanhoa,dc=local
objectClass: top
objectClass: dcObject
objectclass: organization
o: Nhanhoa Software
dc: Nhahoa

dn: cn=Manager,dc=nhanhoa,dc=local
objectClass: organizationalRole
cn: Manager
description: Directory Manager

dn: ou=People,dc=nhanhoa,dc=local
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=nhanhoa,dc=local
objectClass: organizationalUnit
ou: Group
EOF
```

Update thông tin của basedomain:

```sh
ldapadd -x -D cn=Manager,dc=srv,dc=world -W -f basedomain.ldif 
Enter LDAP Password: # password của manager
```

Sau khi thực hiện xong các bước chúng ta sử dụng lệnh sau để kiểm tra các entry:

```sh
slapcat
```

Để thêm mới một entry chúng ta cần tạo ra file ldif và update thông tin các file ldif đó và dùng `slapcat` để kiểm tra.

Ví dụ về thêm một entry user :

```sh
cat > adduser_1.ldif << EOF
dn: cn=adduser_1,ou=People,dc=nhanhoa,dc=local
objectClass: person
objectClass: inetOrgPerson
userPassword:: V2VsY29tZTEyMw==
sn: user
cn: adduser_1
EOF
```

Update file `adduser_1.ldif` để thông tin user được thêm vào cây LDAP :

```sh
ldapadd -x -D cn=Manager,dc=nhanhoa,dc=local -W -f adduser_1.ldif
```



## Tổng kết.

Ở bài này mình đã hướng dẫn mọi người cách để cài đặt OpenLDAP trên CentOS, ở bài sau mình sẽ hướng dẫn mọi người cài `PHPLDAPADMIN`, một công cụ giúp chúng ta có thể thao tác dễ dàng trên slapd hơn. Cảm ơn đã ghé thăm Cloud365 !

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>