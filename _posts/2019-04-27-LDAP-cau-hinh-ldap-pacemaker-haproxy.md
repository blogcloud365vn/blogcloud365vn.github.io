---
date: 2019-04-27
title: "Hướng dẫn triển khai LDAP Multi Master Haproxy Pacemaker trên CentOS 7"
categories:
  - Linux
description: Hướng dẫn triển khai LDAP Multi-Master Haproxy Pacemaker trên CentOS 7
author: thanhnb
tags: [LDAP, Cluster, Load balancer, High Availability, HAproxy, Database, Linux, CentOS7]
type: Document
---

## Tổng quan

Trong bài blog này, mình sẽ hướng dẫn các bạn cách cấu hình LDAP ở chế độ Multi-Master Master. Mô hình Multi-Master được sử dụng nhằm tăng khả năng mở rộng, tăng hiệu suất đọc ghi dữ liệu vào dịch vụ ldap. Bên cạnh đó, mình sẽ áp dụng Pacemaker và HAProxy để điều phối cân bằng tải dịch vụ giữa các node Master, tăng sự sẵn sàng của dịch vụ, cải thiện khả năng chịu lỗi và vượt qua lỗi trên dịch vụ LDAP.

Mô hình bên dưới rất phức tạp, các bạn tham khảo thêm tại:
- [Chuỗi bài viết về HAProxy](https://blog.cloud365.vn/search/?q=haproxy)
- [Chuỗi bài viết về High Availability](https://blog.cloud365.vn/search/?q=High+Availability)
- [Chuỗi bài viết về LDAP]()

## Mô hình

### Mô hình triển khai

![](/images/img-ldap-master-pacemaker/pic2.png)

### Mô hình quản trị dịch vụ bằng pacemaker

![](/images/img-ldap-master-pacemaker/pic1.png)

## Chuẩn bị
- Cài đặt 3 node LDAP với IP như sau

```
HOSTNAME    ldap_94 (LDAP Master 94)
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: network access (10.10.10.94)

HOSTNAME    ldap_95 (LDAP Master 95)
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: network access (10.10.10.95)

HOSTNAME    ldap_96 (LDAP Master 96)
CPU         2 core
RAM         2 GB
Disk        vda: os
Network     eth0: network access (10.10.10.96)
```

### Bổ sung config tại tất cả các node

Cấu hình file host
```
echo '
10.10.10.94 ldap_94 
10.10.10.95 ldap_95 
10.10.10.96 ldap_96' >> /etc/hosts
```

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

## Cài đặt mô hình Master Master 3 Node
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

### Tại node LDAP Master 94

Trên node LDAP Master 94, cấu hình cho phép nhân bản dữ liệu lên node LDAP Master 95 và LDAP Master 96

Lưu ý, mình sẽ cấu hình olcServerID theo thứ tự:
- LDAP Master 94, olcServerID = 1
- LDAP Master 95, olcServerID = 2
- LDAP Master 96, olcServerID = 3

Lưu ý: Tại node LDAP Master 94, ta sẽ cấu hình cho phép nhân bản dữ liệu từ node LDAP Master 95 và LDAP Master 96


Cấu hình cho phép nhân bản dữ liệu từ node 95, 96

```
cat > master.ldif << EOF
dn: cn=config
changetype: modify
replace: olcServerID
olcServerID: 1 ldap://10.10.10.94:389
olcServerID: 2 ldap://10.10.10.95:389
olcServerID: 3 ldap://10.10.10.96:389


dn: olcDatabase={2}hdb,cn=config
changetype: modify
add: olcSyncRepl
olcSyncRepl: rid=002
  provider=ldap://10.10.10.95:389/
  bindmethod=simple
  binddn="cn=Manager,dc=nhanhoa,dc=local"
  credentials=Cloud365a@123
  searchbase="dc=nhanhoa,dc=local"
  scope=sub
  schemachecking=on
  type=refreshAndPersist
  retry="30 5 300 3"
  interval=00:00:05:00
olcSyncRepl: rid=003
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

ldapmodify -Y EXTERNAL -H ldapi:/// -f master.ldif
```

Thay đổi settings sysconfig của slapd

```
cat > /etc/sysconfig/slapd << EOF
# OpenLDAP server configuration
# see 'man slapd' for additional information

# Where the server will run (-h option)
# - ldapi:/// is required for on-the-fly configuration using client tools
#   (use SASL with EXTERNAL mechanism for authentication)
# - default: ldapi:/// ldap:///
# - example: ldapi:/// ldap://127.0.0.1/ ldap://10.0.0.1:1389/ ldaps:///
SLAPD_URLS="ldapi:/// ldap://10.10.10.94:389"

# Any custom options
#SLAPD_OPTIONS=""

# Keytab location for GSSAPI Kerberos authentication
#KRB5_KTNAME="FILE:/etc/openldap/ldap.keytab"
EOF
```

Khởi động lại dịch vụ (Bước quan trọng, để LDAP cập nhật lại trạng thái dịch vụ)
```
systemctl restart slapd
```

### Tại node LDAP Master 95

Lưu ý: Tại node LDAP Master 95, ta sẽ cấu hình cho phép nhân bản dữ liệu từ node LDAP Master 94 và LDAP Master 96

Cấu hình cho phép nhân bản dữ liệu từ node 94, 96

```
cat > master.ldif << EOF
dn: cn=config
changetype: modify
replace: olcServerID
olcServerID: 1 ldap://10.10.10.94:389
olcServerID: 2 ldap://10.10.10.95:389
olcServerID: 3 ldap://10.10.10.96:389


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
olcSyncRepl: rid=003
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

ldapmodify -Y EXTERNAL -H ldapi:/// -f master.ldif
```

Thay đổi settings sysconfig của slapd

```
cat > /etc/sysconfig/slapd << EOF
# OpenLDAP server configuration
# see 'man slapd' for additional information

# Where the server will run (-h option)
# - ldapi:/// is required for on-the-fly configuration using client tools
#   (use SASL with EXTERNAL mechanism for authentication)
# - default: ldapi:/// ldap:///
# - example: ldapi:/// ldap://127.0.0.1/ ldap://10.0.0.1:1389/ ldaps:///
SLAPD_URLS="ldapi:/// ldap://10.10.10.95:389"

# Any custom options
#SLAPD_OPTIONS=""

# Keytab location for GSSAPI Kerberos authentication
#KRB5_KTNAME="FILE:/etc/openldap/ldap.keytab"
EOF
```

Khởi động lại dịch vụ (Bước quan trọng, để LDAP cập nhật lại trạng thái dịch vụ)
```
systemctl restart slapd
```

### Tại node LDAP Master 96

Lưu ý: Tại node LDAP Master 94, ta sẽ cấu hình cho phép nhân bản dữ liệu từ node LDAP Master 94 và LDAP Master 95

Cấu hình cho phép nhân bản dữ liệu từ node 94, 95

```
cat > master.ldif << EOF
dn: cn=config
changetype: modify
replace: olcServerID
olcServerID: 1 ldap://10.10.10.94:389
olcServerID: 2 ldap://10.10.10.95:389
olcServerID: 3 ldap://10.10.10.96:389


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
olcSyncRepl: rid=002
  provider=ldap://10.10.10.95:389/
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

ldapmodify -Y EXTERNAL -H ldapi:/// -f master.ldif
```

Thay đổi settings sysconfig của slapd

```
cat > /etc/sysconfig/slapd << EOF
# OpenLDAP server configuration
# see 'man slapd' for additional information

# Where the server will run (-h option)
# - ldapi:/// is required for on-the-fly configuration using client tools
#   (use SASL with EXTERNAL mechanism for authentication)
# - default: ldapi:/// ldap:///
# - example: ldapi:/// ldap://127.0.0.1/ ldap://10.0.0.1:1389/ ldaps:///
SLAPD_URLS="ldapi:/// ldap://10.10.10.96:389"

# Any custom options
#SLAPD_OPTIONS=""

# Keytab location for GSSAPI Kerberos authentication
#KRB5_KTNAME="FILE:/etc/openldap/ldap.keytab"
EOF
```

Khởi động lại dịch vụ (Bước quan trọng, để LDAP cập nhật lại trạng thái dịch vụ)
```
systemctl restart slapd
```

## Cấu hình HTTP

Thực hiện tại LDAP Admin 94
```
sed -i "s/Listen 80/Listen 10.10.10.94:80/g" /etc/httpd/conf/httpd.conf
systemctl stop httpd
systemctl disable httpd
```

Thực hiện tại LDAP Admin 95
```
sed -i "s/Listen 80/Listen 10.10.10.95:80/g" /etc/httpd/conf/httpd.conf
systemctl stop httpd
systemctl disable httpd
```

Thực hiện tại LDAP Admin 96
```
sed -i "s/Listen 80/Listen 10.10.10.96:80/g" /etc/httpd/conf/httpd.conf
systemctl stop httpd
systemctl disable httpd
```

## Chỉnh sửa cấu hình PHP LDAP ADMIN 

Tại cả 3 node LDAP 94 95 96, thực hiện cấu hình sau:
```
cp /usr/share/phpldapadmin/config/config.php /usr/share/phpldapadmin/config/config.php.bak

cat > /usr/share/phpldapadmin/config/config.php << 'EOF'
<?php

$config->custom->session['blowfish'] = '626afa88f36a312c581b8af8ca20a398';  # Autogenerated for ldap_94

$config->custom->appearance['friendly_attrs'] = array(
	'facsimileTelephoneNumber' => 'Fax',
	'gid'                      => 'Group',
	'mail'                     => 'Email',
	'telephoneNumber'          => 'Telephone',
	'uid'                      => 'User Name',
	'userPassword'             => 'Password'
);

$servers = new Datastore();

$servers->newServer('ldap_pla');
$servers->setValue('server','name','LDAP VIP');
$servers->setValue('server','host','10.10.10.97');
$servers->setValue('server','port',389);
$servers->setValue('server','base',array('dc=nhanhoa,dc=local'));
$servers->setValue('login','auth_type','cookie');
$servers->setValue('login','bind_id','');
$servers->setValue('login','bind_pass','');
$servers->setValue('server','tls',false);

$servers->newServer('ldap_pla');
$servers->setValue('server','name','LDAP ADMIN 94');
$servers->setValue('server','host','10.10.10.94');
$servers->setValue('server','port',389);
$servers->setValue('server','base',array('dc=nhanhoa,dc=local'));
$servers->setValue('login','auth_type','cookie');
$servers->setValue('login','bind_id','');
$servers->setValue('login','bind_pass','');
$servers->setValue('server','tls',false);

$servers->newServer('ldap_pla');
$servers->setValue('server','name','LDAP ADMIN 95');
$servers->setValue('server','host','10.10.10.95');
$servers->setValue('server','port',389);
$servers->setValue('server','base',array('dc=nhanhoa,dc=local'));
$servers->setValue('login','auth_type','cookie');
$servers->setValue('login','bind_id','');
$servers->setValue('login','bind_pass','');
$servers->setValue('server','tls',false);

$servers->newServer('ldap_pla');
$servers->setValue('server','name','LDAP ADMIN 96');
$servers->setValue('server','host','10.10.10.96');
$servers->setValue('server','port',389);
$servers->setValue('server','base',array('dc=nhanhoa,dc=local'));
$servers->setValue('login','auth_type','cookie');
$servers->setValue('login','bind_id','');
$servers->setValue('login','bind_pass','');
$servers->setValue('server','tls',false);
?>
EOF
```

Tắt dịch vụ HTTP (Dịch vụ HTTP sẽ được xử lý bởi pacemaker)

```
systemctl stop httpd
systemctl disable httpd
```

## Triển khai HAProxy

> Thực hiện trên tất cả các node

Cài đặt
```
sudo yum install wget socat -y
wget http://cbs.centos.org/kojifiles/packages/haproxy/1.8.1/5.el7/x86_64/haproxy18-1.8.1-5.el7.x86_64.rpm 
yum install haproxy18-1.8.1-5.el7.x86_64.rpm -y
```

Tạo bản backup cho cấu hình mặc định và chỉnh sửa cấu hình HAproxy
```
cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak
```

Cầu hình Haproxy
```
cat > /etc/haproxy/haproxy.cfg << EOF
global
    log         127.0.0.1 local2
    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     4000
    user        haproxy
    group       haproxy
    daemon
    stats socket /var/lib/haproxy/stats

defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 3
    timeout http-request    10s
    timeout queue           1m
    timeout connect         10s
    timeout client          1m
    timeout server          1m
    timeout http-keep-alive 10s
    timeout check           10s
    maxconn                 3000

listen stats
    bind :8080
    mode http
    stats enable
    stats uri /stats
    stats realm HAProxy\ Statistics
    stats admin if TRUE

listen ldap_cluster
    bind 10.10.10.97:389
    balance roundrobin
    stick-table type ip size 200k expire 30m
    stick on src
    mode tcp
    option tcplog
    timeout client 28801s
    timeout server 28801s
    server ldap_94 10.10.10.94:389 check inter 5s fastinter 2s rise 3 fall 3
    server ldap_95 10.10.10.95:389 check inter 5s fastinter 2s rise 3 fall 3
    server ldap_96 10.10.10.96:389 check inter 5s fastinter 2s rise 3 fall 3

listen web_backend
    bind 10.10.10.97:80
    balance  roundrobin
    cookie SERVERID insert indirect nocache
    mode  http
    option  httpchk
    option  httpclose
    option  httplog
    option  forwardfor
    server web_ldap_94 10.10.10.94:80 check cookie ldap_94 inter 5s fastinter 2s rise 3 fall 3
    server web_ldap_95 10.10.10.95:80 check cookie ldap_95 inter 5s fastinter 2s rise 3 fall 3
    server web_ldap_96 10.10.10.96:80 check cookie ldap_96 inter 5s fastinter 2s rise 3 fall 3
EOF
```

Cấu hình Log cho HAProxy

```
sed -i "s/#\$ModLoad imudp/\$ModLoad imudp/g" /etc/rsyslog.conf
sed -i "s/#\$UDPServerRun 514/\$UDPServerRun 514/g" /etc/rsyslog.conf
echo '$UDPServerAddress 127.0.0.1' >> /etc/rsyslog.conf

echo 'local2.*    /var/log/haproxy.log' > /etc/rsyslog.d/haproxy.conf

systemctl restart rsyslog
```

Bổ sung cấu hình cho phép kernel có thể binding tới IP VIP

```
echo 'net.ipv4.ip_nonlocal_bind = 1' >> /etc/sysctl.conf
```

Kiểm tra
```
$ sysctl -p

net.ipv4.ip_nonlocal_bind = 1
```

Khởi tạo dịch vụ HAProxy
```
systemctl stop haproxy
systemctl disable haproxy
```

## Thiết lập Cluster Pacemaker

### Bước 1: Cài đặt pacemaker corosync

> Thực hiện trên tất cả các node

Cài đặt gói pacemaker pcs

```
yum -y install pacemaker pcs

systemctl start pcsd 
systemctl enable pcsd
```

Thiết lập mật khẩu user hacluster

```
passwd hacluster
```

### Bước 2: Tạo Cluster
> Thực hiện trên 1 node duy nhất ldap_94

Chứng thực các node
```
pcs cluster auth ldap_94 ldap_95 ldap_96
```

Kết quả
```
[root@ldap_94 ~]# pcs cluster auth ldap_94 ldap_95 ldap_96
Username: hacluster
Password: *********
ldap_96: Authorized
ldap_94: Authorized
ldap_95: Authorized
```

Khởi tạo cấu hình cluster ban đầu
```
pcs cluster setup --name ha_cluster ldap_94 ldap_95 ldap_96
```

Kết quả
```
[root@ldap_94 ~]# pcs cluster setup --name ha_cluster ldap_94 ldap_95 ldap_96

Destroying cluster on nodes: ldap_94, ldap_95, ldap_96...
ldap_95: Stopping Cluster (pacemaker)...
ldap_94: Stopping Cluster (pacemaker)...
ldap_96: Stopping Cluster (pacemaker)...
ldap_95: Successfully destroyed cluster
ldap_94: Successfully destroyed cluster
ldap_96: Successfully destroyed cluster

Sending 'pacemaker_remote authkey' to 'ldap_94', 'ldap_95', 'ldap_96'
ldap_94: successful distribution of the file 'pacemaker_remote authkey'
ldap_96: successful distribution of the file 'pacemaker_remote authkey'
ldap_95: successful distribution of the file 'pacemaker_remote authkey'
Sending cluster config files to the nodes...
ldap_94: Succeeded
ldap_95: Succeeded
ldap_96: Succeeded

Synchronizing pcsd certificates on nodes ldap_94, ldap_95, ldap_96...
ldap_96: Success
ldap_94: Success
ldap_95: Success
Restarting pcsd on the nodes in order to reload the certificates...
ldap_96: Success
ldap_94: Success
ldap_95: Success
```

Khởi động Cluster
```
pcs cluster start --all
```

Kết quả
```
[root@ldap_94 ~]# pcs cluster start --all

ldap_94: Starting Cluster (corosync)...
ldap_95: Starting Cluster (corosync)...
ldap_96: Starting Cluster (corosync)...
ldap_95: Starting Cluster (pacemaker)...
ldap_94: Starting Cluster (pacemaker)...
ldap_96: Starting Cluster (pacemaker)...
```

Cho phép cluster khởi động cùng OS

```
pcs cluster enable --all 
```

Kết quả
```
[root@ldap_94 ~]# pcs cluster enable --all 

ldap_94: Cluster Enabled
ldap_95: Cluster Enabled
ldap_96: Cluster Enabled
```

### Bước 3: Thiết lập Cluster

Bỏ qua cơ chế STONITH
```
pcs property set stonith-enabled=false
```

Cho phép Cluster chạy kể cả khi mất quorum
```
pcs property set no-quorum-policy=ignore
```

Hạn chế Resource trong cluster chuyển node sau khi Cluster khởi động lại

```
pcs property set default-resource-stickiness="INFINITY"
```

Tạo Resource IP VIP Cluster

```
pcs resource create Virtual_IP ocf:heartbeat:IPaddr2 ip=10.10.10.97 cidr_netmask=24 op monitor interval=30s
```

Tạo Resource quản trị dịch vụ HAProxy
```
pcs resource create Loadbalancer_HaProxy systemd:haproxy op monitor timeout="5s" interval="5s"
```

Tạo Resource quản trị dịch vụ HTTPD
```
pcs resource create Web_Cluster systemd:httpd op monitor timeout="5s" interval="5s"
pcs resource clone Web_Cluster globally-unique=true
```

Ràng buộc thứ tự khởi động dịch vụ, khởi động dịch vụ Virtual_IP sau đó khởi động dịch vụ Loadbalancer_HaProxy

```
pcs constraint order start Virtual_IP then Loadbalancer_HaProxy kind=Optional
```

Ràng buộc resource Virtual_IP phải khởi động cùng node với resource Loadbalancer_HaProxy
```
pcs constraint colocation add Virtual_IP Loadbalancer_HaProxy INFINITY
```

Ràng buộc thứ tự khởi động dịch vụ, khởi động dịch vụ Virtual_IP sau đó khởi động dịch vụ Web_Cluster
```
pcs constraint order Virtual_IP then Web_Cluster-clone
```


## Kiểm tra dịch vụ

### Kiểm tra dịch vụ HAProxy

![](/images/img-ldap-master-pacemaker/pic3.png)

### Thêm dữ liệu vào LDAP
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

ldapadd -x -h 10.10.10.97 -D cn=Manager,dc=nhanhoa,dc=local -W -f user_test_thanhbaba.ldif
```

Kết quả

```
adding new entry "cn=thanhbaba,ou=Tests,dc=nhanhoa,dc=local"
```

Kiểm tra dữ liệu trên node Master 94

```
[root@ldap_94 ~]# ldapsearch -x -h 10.10.10.97 -b "dc=nhanhoa,dc=local" -s sub "(objectclass=person)"
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

### Truy vấn dữ liệu từ LDAP

Thử truy vấn dữ liệu bằng ldapclient
```
ldapsearch -x -h 10.10.10.97 -b "dc=nhanhoa,dc=local" -s sub "(ou=*)"
```

Kết quả
```
[root@ldap_94 ~]# ldapsearch -x -h 10.10.10.97 -b "dc=nhanhoa,dc=local" -s sub "(ou=*)"
# extended LDIF
#
# LDAPv3
# base <dc=nhanhoa,dc=local> with scope subtree
# filter: (ou=*)
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

# search result
search: 2
result: 0 Success

# numResponses: 3
# numEntries: 2
```

### Kiểm tra failover

Tắt 1 LDAP Master 94, check HAProxy stats page

```
[root@ldap_94 ~]# init 0
```

Trạng thái HAProxy stats page

![](/images/img-ldap-master-pacemaker/pic4.png)


Ghi dữ liệu vào LDAP qua IP VIP

```
echo '
dn: cn=thanhbaba6,ou=People,dc=nhanhoa,dc=local
objectClass: person
objectClass: inetOrgPerson
mail: thanhbaba6@test.com
userPassword:: b3BlbnN0YWNr
cn: thanhbaba6
sn: thanhbaba6' > user_test_thanhbaba6.ldif

ldapadd -x -h 10.10.10.97 -D cn=Manager,dc=nhanhoa,dc=local -W -f user_test_thanhbaba6.ldif
```

Kết quả
```
[root@ldap_95 ~]# ldapadd -x -h 10.10.10.97 -D cn=Manager,dc=nhanhoa,dc=local -W -f user_test_thanhbaba6.ldif
Enter LDAP Password: 
adding new entry "cn=thanhbaba6,ou=People,dc=nhanhoa,dc=local"
```

Sang node LDAP Master 96
```
ldapsearch -x -h 10.10.10.97 -b "dc=nhanhoa,dc=local" -s sub "(cn=thanhbaba6)"
```

Kết quả
```
[root@ldap_96 ~]# ldapsearch -x -h 10.10.10.97 -b "dc=nhanhoa,dc=local" -s sub "(cn=thanhbaba6)"
# extended LDIF
#
# LDAPv3
# base <dc=nhanhoa,dc=local> with scope subtree
# filter: (cn=thanhbaba6)
# requesting: ALL
#

# thanhbaba6, People, nhanhoa.local
dn: cn=thanhbaba6,ou=People,dc=nhanhoa,dc=local
objectClass: person
objectClass: inetOrgPerson
mail: thanhbaba6@test.com
cn: thanhbaba6
sn: thanhbaba6

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

Dữ liệu đọc ghi bình thường, cụm LDAP không xảy ra vấn đề gì khi 1 node gặp sự cố

## Nguồn

https://blog.cloud365.vn/search/?q=haproxy

https://blog.cloud365.vn/search/?q=High+Availability

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>