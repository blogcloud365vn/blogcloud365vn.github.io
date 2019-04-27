---
date: 2019-04-27
title: "Hướng dẫn cấu hình Keepalive cho mô hình LDAP Master Master"
categories:
  - Linux
description: Hướng dẫn cấu hình Keepalive cho mô hình LDAP Master Master
author: thanhnb
tags: [LDAP, Linux, CentOS7]
type: Document
---

## Mô hình

![](/images/img-ldap-master-master-keepalived/pic1.png)

## Chuẩn bị
- Cài đặt 2 node LDAP với IP như sau

```
HOSTNAME    ldap_94 (LDAP Master 94)
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: network access (10.10.10.94)

HOSTNAME    ldap_96 (LDAP Master 96)
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: network access (10.10.10.96)
```

- Cài đặt mô hình theo docs "Triển khai LDAP Master - Master"

## Cài đặt

### Tại node Master LDAP 94

Cài đặt keepalived
```
yum install keepalived -y
```

Cấu hình keepalived
```
cat > /etc/keepalived/keepalived.conf << EOF
vrrp_script chk_ldap {
    script "/usr/sbin/pidof slapd"
    interval 2
    weight 2
}

vrrp_instance VI_1 {
    interface eth0
    state MASTER
    virtual_router_id 51
    priority 101
    virtual_ipaddress {
        10.10.10.97/24
    }
    track_script {
        chk_ldap
    }
}
EOF
```

Khởi động lại Keepalived
```
systemctl restart keepalived
```

Lưu ý:
- Để hiểu thêm về các tham số cấu hình Keepalive, các bạn tham khảo [link sau](https://blog.cloud365.vn/linux/haproxy-keepalived-apache/)

### Tại node Master LDAP 96

Cài đặt keepalived
```
yum install keepalived -y
```

Cấu hình keepalived
```
cat > /etc/keepalived/keepalived.conf << EOF
vrrp_script chk_ldap {
    script "/usr/sbin/pidof slapd"
    interval 2
    weight 2
}
vrrp_instance VI_1 {
    interface eth0
    state BACKUP
    virtual_router_id 51
    priority 100
    virtual_ipaddress {
        10.10.10.97/24
    }
    track_script {
        chk_ldap
    }
}
EOF
```

Khởi động lại Keepalived
```
systemctl restart keepalived
```

Lưu ý:
- Để hiểu thêm về các tham số cấu hình Keepalive, các bạn tham khảo [link sau](https://blog.cloud365.vn/linux/haproxy-keepalived-apache/)

## Kiểm tra

### Kiểm tra IP VIP

Kiểm tra tại node LDAP Master 94, mặc định Node LDAP Master 94 sẽ nắm IP VIP
```
ip a | grep eth0 -A 2
```

Kết quả
```
[root@ldap_94 keepalived]# ip a | grep eth0 -A 2
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:aa:18:a4 brd ff:ff:ff:ff:ff:ff
    inet 10.10.10.94/24 brd 10.10.10.255 scope global noprefixroute eth0
       valid_lft forever preferred_lft forever
    inet 10.10.10.97/24 scope global secondary eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::af05:e497:90d6:44f7/64 scope link noprefixroute 
```

Kiểm tra tại node LDAP Master 96
```
ip a | grep eth0 -A 2
```

Kết quả
```
[root@ldap_96 keepalived]# ip a | grep eth0 -A 2
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:b5:c2:6b brd ff:ff:ff:ff:ff:ff
    inet 10.10.10.96/24 brd 10.10.10.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:feb5:c26b/64 scope link 
```

### Kiểm tra Failover

Tắt Node LDAP Master 94, kiểm tra IP VIP thay đổi
```
[root@ldap_94 ~]# init 0
```

Trở lại kiểm tra Node LDAP Master 96
```
ip a | grep eth0 -A 2
```

Kết quả
```
[root@ldap_96 ~]# ip a | grep eth0 -A 2
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:b5:c2:6b brd ff:ff:ff:ff:ff:ff
    inet 10.10.10.96/24 brd 10.10.10.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet 10.10.10.97/24 scope global secondary eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:feb5:c26b/64 scope link 
```

Tại node LDAP Master 96, truy vấn dữ liệu bằng IP VIP

```
ldapsearch -x -h 10.10.10.97 -b "dc=nhanhoa,dc=local" -s sub "(objectclass=person)"
```

Kết quả
```
[root@ldap_96 ~]# ldapsearch -x -h 10.10.10.97 -b "dc=nhanhoa,dc=local" -s sub "(objectclass=person)"
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

# numResponses: 3
# numEntries: 2
```

## Nguồn

https://blog.cloud365.vn/linux/haproxy-keepalived-apache/

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>