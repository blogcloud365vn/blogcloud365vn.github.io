---
date: 2019-02-15
title: Cài đặt BIND 9 trên CentOS 7
categories:
  - Linux
description: Cài đặt BIND 9 trên CentOS 7
author: tuanda
tags: [DNS, Linux, Windows]
type: Document
---

## Giới thiệu

Sau khi tìm hiểu sơ lược về các bản ghi trên một hệ thống DNS, bài viết này sẽ hướng dẫn các bạn cài đặt kiểm thử một hệ thống DNS trên CentOS 7 và sử dụng phần mềm BIND 9. 

Mô hình bao gồm 2 máy chủ: DNS server đóng vai trò để phân giải tên miền và client dùng để truy vấn bản ghi tên miền:

<span style="display:block;text-align:center">![](/images/img-bind/bind_2.png)</span>

Trong trường hợp này cả 2 máy đều cài hệ điều hành CentOS 7. Client sẽ truy vấn thông tin về máy chủ có tên là a.tuanda.com và DNS server sẽ thực hiện việc phân giải IP và trả lại cho Client:

<span style="display:block;text-align:center">![](/images/img-bind/bind_1.png)</span>

## Cài đặt BIND

Hướng dẫn bên dưới sẽ thực hiện việc cài đặt BIND thông qua `yum` trên server `CentOS 7.6.1810` :

Thực hiện việc update OS, cài đặt Bind và các gói phụ trợ:

```sh
yum update -y
yum -y install bind bind-utils
```

Sau khi cài đặt xong, kiểm tra lại phiên bản của BIND:

```sh
[root@dns-test ~]# named -v
BIND 9.9.4-RedHat-9.9.4-73.el7_6 (Extended Support Version)
```

Thực hiện việc disable `SELinux`:
```sh
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config

```

Phân quyền
```sh
chgrp named -R /var/named
chown -v root:named /etc/named.conf
```

Tạo zone forward:

```sh
cat /var/named/tuanda.fwd.zone 
$TTL 86400
@       IN SOA  masterdns.tuanda.com.     root.tuanda.com. (
                                  2014090401    ; serial
                                        3600    ; refresh
                                        1800    ; retry
                                      604800    ; expire
                                       86400 )  ; minimum

; Name server's

@           IN      NS      masterdns.tuanda.com.

; Name server hostname to IP resolve.

@           IN      A       172.16.4.243

; Hosts in this Domain

@           IN      A       172.16.4.243
masterdns   IN      A       172.16.4.243
a           IN      A       172.16.4.242
mail        IN      A       172.16.4.242
b           IN      A       172.16.4.241
; mail server
@           IN      MX 10   mail

;CNAME
www         IN      CNAME   a

;TXT
@           IN      TXT     "Day la ban ghi TXT"
```

Tạo zone reverse (bản ghi PTR):

```sh
[root@dns-test ~]# cat /var/named/tuanda.rev.zone 
$TTL 86400
@       IN SOA  masterdns.tuanda.com. root.tuanda.com. (
                                2014090402      ; serial
                                      3600      ; refresh
                                      1800      ; retry
                                    604800      ; expire
                                     86400 )    ; minimum

; Name server's

@               IN      NS      masterdns.tuanda.com.

; Name server hostname to IP resolve.

masterdns       IN      A       172.16.4.243

;Hosts in Domain 

243             IN      PTR   masterdns.tuanda.com.
242             IN      PTR   a.tuanda.com.
241             IN      PTR   b.tuanda.com.
```

Thêm quyền cho file zone:

```sh
sudo chgrp named /var/named/tuanda.fwd.zone
sudo chgrp named /var/named/tuanda.rev.zone
```

Chỉnh sửa cấu hình của service cho phù hợp `/etc/named.conf` :

```sh
options {
        listen-on port 53 { any; };
        listen-on-v6 port 53 { ::1; };

        # Thư mục service làm việc
        directory       "/var/named";
        # Thư mục cache
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        recursing-file  "/var/named/data/named.recursing";
        secroots-file   "/var/named/data/named.secroots";
        # Cho phép query DNS từ dải IP nào hoặc tất cả các dải
        allow-query     { localhost; 103.28.36.0/20;};
        # transfer range ( điều chỉnh nếu có Secondary DNS)
        # allow-transfer      { localhost; 10.0.0.0/24; };

        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification
           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes;

#       dnssec-enable yes;
#       dnssec-validation yes;
        dnssec-enable no;
        dnssec-validation no;

        /* Path to ISC DLV key */
        bindkeys-file "/etc/named.iscdlv.key";

        managed-keys-directory "/var/named/dynamic";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
        type hint;
        file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

#Thêm bên dưới phần cấu hình Zone forward và Zone reverse, giả định là domain "tuanda.com":

zone "tuanda.com" IN {
type master;
file "tuanda.fwd.zone";
# đường dẫn tuyệt đối hoặc tương đối
allow-update { none; };
};

zone "4.16.172.in-addr.arpa" IN {
type master;
file "tuanda.rev.zone";
allow-update { none; };
};
```

Bật service và allow port trên firewalld:

```sh
systemctl start named 
systemctl enable named
firewall-cmd --add-service=dns --permanent 
firewall-cmd --reload 
```

**Cấu hình security với bind-chroot (tùy chọn)**

Cài đặt gói bind-chroot nhằm cô lập các file liên quan đến service BIND 9 với file system

```sh
yum -y install bind-chroot
[root@dlp ~]# /usr/libexec/setup-named-chroot.sh /var/named/chroot on 
[root@dlp ~]# systemctl stop named 
[root@dlp ~]# systemctl disable named 
[root@dlp ~]# systemctl start named-chroot 
[root@dlp ~]# systemctl enable named-chroot 
Created symlink from /etc/systemd/system/multi-user.target.wants/named-chroot.service to /usr/lib/systemd/system/named-chroot.service.

[root@dns-test ~]# ll /var/named/chroot/var/named/
total 40
drwxr-x--- 7 root  named 4096 Feb 20 11:56 chroot
drwxrwx--- 2 named named 4096 Feb 17 03:34 data
drwxrwx--- 2 named named 4096 Feb 20 11:57 dynamic
-rw-r----- 1 root  named 2281 May 22  2017 named.ca
-rw-r----- 1 root  named  152 Dec 15  2009 named.empty
-rw-r----- 1 root  named  152 Jun 21  2007 named.localhost
-rw-r----- 1 root  named  168 Dec 15  2009 named.loopback
drwxrwx--- 2 named named 4096 Jan 30 00:23 slaves
-rw-r--r-- 1 root  named  995 Feb 12 17:51 tuanda.fwd.zone
-rw-r--r-- 1 root  root   863 Feb 12 17:01 tuanda.rev.zone
[root@dns-test ~]# ll /var/named/chroot/etc
total 696
-rw-r--r-- 2 root root     361 Jan  9 03:25 localtime
drwxr-x--- 2 root named   4096 Jan 30 00:23 named
-rw-r----- 1 root named   2376 Feb 12 17:16 named.conf
-rw-r--r-- 1 root named   3923 Jan 30 00:23 named.iscdlv.key
-rw-r----- 1 root named    931 Jun 21  2007 named.rfc1912.zones
-rw-r--r-- 1 root named   1587 May 22  2017 named.root.key
drwxr-x--- 3 root named   4096 Feb 20 11:56 pki
-rw-r--r-- 1 root root    6545 Oct 31 02:48 protocols
-rw-r----- 1 root named    100 Feb 12 14:55 rndc.key
-rw-r--r-- 1 root root  670293 Jun  7  2013 services
[root@dns-test ~]# ll /var/named/chroot/var/named 
total 40
drwxr-x--- 7 root  named 4096 Feb 20 11:56 chroot
drwxrwx--- 2 named named 4096 Feb 17 03:34 data
drwxrwx--- 2 named named 4096 Feb 20 11:57 dynamic
-rw-r----- 1 root  named 2281 May 22  2017 named.ca
-rw-r----- 1 root  named  152 Dec 15  2009 named.empty
-rw-r----- 1 root  named  152 Jun 21  2007 named.localhost
-rw-r----- 1 root  named  168 Dec 15  2009 named.loopback
drwxrwx--- 2 named named 4096 Jan 30 00:23 slaves
-rw-r--r-- 1 root  named  995 Feb 12 17:51 tuanda.fwd.zone
-rw-r--r-- 1 root  root   863 Feb 12 17:01 tuanda.rev.zone
```

Verify lại các file Zone:
```sh
named-checkzone tuanda.com /var/named/tuanda.rev.zone 
zone tuanda.com/IN: loaded serial 2014090402
OK
named-checkzone tuanda.com /var/named/tuanda.fwd.zone       
zone tuanda.com/IN: loaded serial 2014090401
OK
```

## Truy vấn bản ghi từ client ##
Tiếp theo để kiểm tra các bản ghi đã tạo, chúng ta sẽ thực hiện truy vấn từ máy client (đã được allow network ở trên)

- Sử dụng DNS server đã setup ở trên để phân giải bằng cách sửa file `/etc/resolv.conf `

```sh
nameserver 103.101.x.x
```

- Sử dụng công cụ `dig` để truy vấn. Có thể cài đặt nhanh bằng lệnh `yum install bind-utils`. Sau khi cài đặt thành công sử dụng tools để truy vấn:

- Truy vấn bản ghi A:

<span style="display:block;text-align:center">![](/images/img-bind/bind_6.png)</span>

- Truy vấn bản ghi phân giải ngược PTR:

<span style="display:block;text-align:center">![](/images/img-bind/bind_7.png)</span>

- Tương tự với các bản ghi CNAME, MX, TXT đã tạo ở trên.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>