---
title: "LDAP [Part 10] - Hướng dẫn triển khai LDAP Master - Master trên CentOS 7"
categories:
  - LDAP
description: Hướng dẫn triển khai LDAP Master - Master trên CentOS 7
author: thanhnb
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 10
---


## Tổng quan

Trong bài blog này, mình sẽ hướng dẫn các bạn cách cấu hình LDAP ở chế độ Master Master. Mô hình Master Master áp dụng trong trường hợp tải cao, yêu cầu ghi vào LDAP nhiều mà trong khi với mô hình Master Slave bạn chỉ có thể ghi duy nhất vào node Master. Với chế độ Master Master, người dùng có thể ghi vào bất kỳ Master nào thuộc cụm LDAP. Đồng thời, giải pháp Master Master LDAP sẽ cải thiện hiệu năng GHI vào LDAP, tăng tính sẵn sàng cho dịch vụ.

## Mô hình

![](/images/img-ldap-master-master/pic1.png)

## Chuẩn bị
- Cằi đặt 2 node LDAP với IP như sau

```
HOSTNAME    ldap_94 (Master)
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: Network access (10.10.10.94)

HOSTNAME    ldap_96 (Master)
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: Network access (10.10.10.96)
```

## Cài đặt

### Bổ sung config tại tất cả các node
Kích hoạt module syncprov

```
cat > mod_syncprov.ldif << EOF
dn: cn=module,cn=config
objectClass: olcModuleList
cn: module
olcModulePath: /usr/lib64/openldap
olcModuleLoad: syncprov.la
EOF

ldapadd -Y EXTERNAL -H ldapi:/// -f mod_syncprov.ldif
```

Kết quả
```
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
adding new entry "cn=module,cn=config"
```

Cho phép module syncprov hoạt động trên DB LDAP
```
cat > syncprov.ldif << EOF
dn: olcOverlay=syncprov,olcDatabase={2}hdb,cn=config
objectClass: olcOverlayConfig
objectClass: olcSyncProvConfig
olcOverlay: syncprov
olcSpSessionLog: 100
EOF

ldapadd -Y EXTERNAL -H ldapi:/// -f syncprov.ldif
```

Kết quả
```
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
adding new entry "olcOverlay=syncprov,olcDatabase={2}hdb,cn=config"
```

### Tại node Master 94

Lưu ý:
- Trong mô hình mình có 2 node, Master 94 và Master 96
- Tại Node Master 94 sẽ khai báo cấu hình cho phép nhân bản dữ liệu lên node Master 96 (master96.ldif)
- Tại Node Master 96 sẽ khai báo cấu hình cho phép nhân bản dữ liệu lên node Master 94 (master94.ldif)


Lưu ý file cấu hình:
- `olcServerID`: Độc nhất, định danh cho node LDAP. Với Master 94, mình định nghĩa `olcServerID: 2`. với Master 96, mình định nghĩa `olcServerID: 1`)
- `provider`: Định nghĩa IP + Port của Note LDAP kết nối tới (ở đây là node Master 96)

Cấu hình

```
cat > master96.ldif << EOF
dn: cn=config
changetype: modify
replace: olcServerID
olcServerID: 1

dn: olcDatabase={2}hdb,cn=config
changetype: modify
add: olcSyncRepl
olcSyncRepl: rid=001
  provider=ldap://10.10.10.96:389/
  bindmethod=simple
  binddn="cn=Manager,dc=nhanhoa,dc=local"
  credentials=Cloud365a@123
  searchbase="dc=nhanhoa,dc=local"
  scope=sub
  schemachecking=on
  type=refreshAndPersist
  retry="30 5 300 3"
  interval=00:00:05:00
-
add: olcMirrorMode
olcMirrorMode: TRUE

dn: olcOverlay=syncprov,olcDatabase={2}hdb,cn=config
changetype: add
objectClass: olcOverlayConfig
objectClass: olcSyncProvConfig
olcOverlay: syncprov
EOF

ldapmodify -Y EXTERNAL -H ldapi:/// -f master96.ldif
```

Kết quả
```
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
modifying entry "cn=config"

modifying entry "olcDatabase={2}hdb,cn=config"

adding new entry "olcOverlay=syncprov,olcDatabase={2}hdb,cn=config"

```

### Tại node Master 96

Lưu ý file cấu hình:
- `olcServerID`: Độc nhất, định danh cho node LDAP. Với Master 94, mình định nghĩa `olcServerID: 2`. với Master 96, mình định nghĩa `olcServerID: 1`)
- `provider`: Định nghĩa IP + Port của Note LDAP kết nối tới (ở đây là node Master 94)

```
cat > master94.ldif << EOF
dn: cn=config
changetype: modify
replace: olcServerID
olcServerID: 2

dn: olcDatabase={2}hdb,cn=config
changetype: modify
add: olcSyncRepl
olcSyncRepl: rid=001
  provider=ldap://10.10.10.94:389/
  bindmethod=simple
  binddn="cn=Manager,dc=nhanhoa,dc=local"
  credentials=Cloud365a@123
  searchbase="dc=nhanhoa,dc=local"
  scope=sub
  schemachecking=on
  type=refreshAndPersist
  retry="30 5 300 3"
  interval=00:00:05:00
-
add: olcMirrorMode
olcMirrorMode: TRUE

dn: olcOverlay=syncprov,olcDatabase={2}hdb,cn=config
changetype: add
objectClass: olcOverlayConfig
objectClass: olcSyncProvConfig
olcOverlay: syncprov
EOF

ldapmodify -Y EXTERNAL -H ldapi:/// -f master94.ldif
```

Kết quả
```
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
modifying entry "cn=config"

modifying entry "olcDatabase={2}hdb,cn=config"

adding new entry "olcOverlay=syncprov,olcDatabase={2}hdb,cn=config"

```

## Kiểm tra

### Thêm dữ liệu vào node master 94

Thêm dữ liệu vào node Master 94
```
echo '
dn: ou=Tests,dc=nhanhoa,dc=local
ou: Tests
objectclass: organizationalUnit' > test_structure.ldif

ldapadd -x -D cn=Manager,dc=nhanhoa,dc=local -W -f test_structure.ldif
```

Kết quả
```
adding new entry "ou=Tests,dc=nhanhoa,dc=local"
```

Kiểm tra dữ liệu trên node Master 96
```
ldapsearch -x -h 10.10.10.96 -b "dc=nhanhoa,dc=local" -s sub "(objectclass=person)"
```

Kết quả
```
[root@ldap_96 ~]# ldapsearch -x -h 10.10.10.96 -b "dc=nhanhoa,dc=local" -s sub "(objectclass=organizationalUnit)"
# extended LDIF
#
# LDAPv3
# base <dc=nhanhoa,dc=local> with scope subtree
# filter: (objectclass=organizationalUnit)
# requesting: ALL
#

# People, nhanhoa.local
dn: ou=People,dc=nhanhoa,dc=local
objectClass: organizationalUnit
ou: People

# Group, nhanhoa.local
dn: ou=Group,dc=nhanhoa,dc=local
objectClass: organizationalUnit
ou: Group

# Tests, nhanhoa.local
dn: ou=Tests,dc=nhanhoa,dc=local
ou: Tests
objectClass: organizationalUnit

# search result
search: 2
result: 0 Success

# numResponses: 4
# numEntries: 3
```

### Thêm dữ liệu vào node master 96

Thêm dữ liệu vào node Master 96

```
echo '
dn: cn=thanhbaba,ou=Tests,dc=nhanhoa,dc=local
objectClass: person
objectClass: inetOrgPerson
mail: thanhbaba@test.com
userPassword:: b3BlbnN0YWNr
cn: thanhbaba
sn: thanhbaba' > user_test_thanhbaba.ldif

ldapadd -x -D cn=Manager,dc=nhanhoa,dc=local -W -f user_test_thanhbaba.ldif
```

Kết quả

```
adding new entry "cn=thanhbaba,ou=Tests,dc=nhanhoa,dc=local"
```

Kiểm tra dữ liệu trên node Master 94

```
[root@ldap_94 ~]# ldapsearch -x -h 10.10.10.94 -b "dc=nhanhoa,dc=local" -s sub "(objectclass=person)"
# extended LDIF
#
# LDAPv3
# base <dc=nhanhoa,dc=local> with scope subtree
# filter: (objectclass=person)
# requesting: ALL
#

# thanhbaba, Tests, nhanhoa.local
dn: cn=thanhbaba,ou=Tests,dc=nhanhoa,dc=local
objectClass: person
objectClass: inetOrgPerson
mail: thanhbaba@test.com
cn: thanhbaba
sn: thanhbaba

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```


### Tắt node Master 94, ghi dữ liệu vào Master 96

Tại node Master 94
```
[root@ldap_94 ~]# init 0
```

Tại node Master 96
```
echo '
dn: cn=thanhbaba2,ou=Tests,dc=nhanhoa,dc=local
objectClass: person
objectClass: inetOrgPerson
mail: thanhbaba2@test.com
userPassword:: b3BlbnN0YWNr
cn: thanhbaba2
sn: thanhbaba2' > user_test_thanhbaba2.ldif

ldapadd -x -D cn=Manager,dc=nhanhoa,dc=local -W -f user_test_thanhbaba2.ldif
```

Kết quả
```
adding new entry "cn=thanhbaba2,ou=Tests,dc=nhanhoa,dc=local"
```

Kiểm tra
```
[root@ldap_96 ~]# ldapsearch -x -h 10.10.10.96 -b "dc=nhanhoa,dc=local" -s sub "(cn=thanhbaba2)"
# extended LDIF
#
# LDAPv3
# base <dc=nhanhoa,dc=local> with scope subtree
# filter: (cn=thanhbaba2)
# requesting: ALL
#

# thanhbaba2, Tests, nhanhoa.local
dn: cn=thanhbaba2,ou=Tests,dc=nhanhoa,dc=local
objectClass: person
objectClass: inetOrgPerson
mail: thanhbaba2@test.com
cn: thanhbaba2
sn: thanhbaba2

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

### Bật node Master 94, kiểm tra dữ liệu đồng bộ

```
[root@ldap_94 ~]# ldapsearch -x -h 10.10.10.94 -b "dc=nhanhoa,dc=local" -s sub "(cn=thanhbaba2)"
# extended LDIF
#
# LDAPv3
# base <dc=nhanhoa,dc=local> with scope subtree
# filter: (cn=thanhbaba2)
# requesting: ALL
#

# thanhbaba2, Tests, nhanhoa.local
dn: cn=thanhbaba2,ou=Tests,dc=nhanhoa,dc=local
objectClass: person
objectClass: inetOrgPerson
mail: thanhbaba2@test.com
cn: thanhbaba2
sn: thanhbaba2

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

## Nguồn

https://www.server-world.info/en/note?os=CentOS_7&p=openldap&f=6

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>