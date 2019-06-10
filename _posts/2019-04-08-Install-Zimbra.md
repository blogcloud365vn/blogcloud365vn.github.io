---
date: 2019-04-08
title: Cài đặt Zimbra trên CentOS 7
categories:
  - Zimbra
description: Tự xây dựng hệ thống email cho cá nhân.
author: minhnv
tags: [Zimbra, CentOS, Mail]
type: Document
---

Bạn là một cá nhân hay một tổ chức, bạn muốn xây dựng email server riêng. Hiện nay có rất nhiều giải pháp email server nhưng trong bài viết này tôi sẽ hướng dẫn bạn toàn trình các bước cài đặt từ việc setup các bản ghi DNS tới việc cài đặt bộ MTA và MDA. 


### Here we go!

## Chuẩn bị 

- Trước tiên bạn muốn tạo một email server yêu cầu bạn cần có một VPS có IP public và một domain name. Các bạn có thể đăng kí các dịch vụ tại <a href="https://cloud365.vn/">Nhân Hòa</a>

- Cấu hình VPS của tôi trong bài viết này: 

    + OS: CentOS 7
    + IP: 203.162.79.139
    + RAM: 4GB 
    + DISK: 100GB 

- Domain name sử dụng:

    + supportdao.com

## Bước 1: Thiết lập bản ghi DNS

- Truy cập vào <a href = "https://zonedns.vn/">trang ZoneDNS</a> để thiết lập các bản ghi

<img src='https://i.imgur.com/tTDH0ak.png'>


| | | | |
|------|------|------|-----|
| mail | A | 203.162.79.139 |
| autodiscover | CNAME | mail.supportdao.com. |
| autoconfig | CNAME | mail.supportdao.com. |
|@	| MX	| mail.supportdao.com.|	10|

## Bước 2: Chuẩn bị môi trường trên máy chủ mail 

- Update

```
yum update -y ; reboot
```

- Sửa hostname

```
hostnamectl set-hostname "mail.supportdao.com"
exec bash
```

- Sửa file /etc/hosts 

```
vim /etc/hosts 
203.162.79.139 mail.supportdao.com mail
```
- Kiểm tra xem có dịch vụ nào đang sử dụng port mà zimbra sử dụng không bằng cách 

- Nếu chưa cài đặt `netstat`

```
yum -y install net-tools
```

```
netstat -tulpn | grep -E -w '25|80|110|143|443|465|587|993|995|5222|5223|9071|7071'
```
- Nếu có service nào đang chạy trên các port trên thì tìm tách tắt đi hoặc thay thế 

- Nếu chưa có `dig` thực hiện cài đặt 

```
yum install bind-utils
```

- Kiểm tra lại bản ghi 

```
dig -t A mail.supportdao.com
dig -t MX supportdao.com
```

## Bước 3: Cài đặt Zimbra 

- Cài đặt packages cần thiết 

```
yum install unzip net-tools sysstat openssh-clients perl-core libaio nmap-ncat libstdc++.so.6 wget -y
```

- Tải và giải nén zimbra 

```
mkdir zimbra && cd zimbra
wget https://files.zimbra.com/downloads/8.8.10_GA/zcs-8.8.10_GA_3039.RHEL7_64.20180928094617.tgz --no-check-certificate
```

Nếu chưa có wget thực hiện: 

```
yum install wget -y
```

- Cài đặt zimbra 

```
tar zxpvf zcs-8.8.10_GA_3039.RHEL7_64.20180928094617.tgz
cd zcs-8.8.10_GA_3039.RHEL7_64.20180928094617
./install.sh
```

- Chon Y 

<img src="https://i.imgur.com/5axfdGs.png">

- Chọn Y 

```
Use Zimbra's package repository [Y] Y
```

- Cài đặt tất cả package 

<img src="https://i.imgur.com/DX9exc0.png">

- Sau khi nhấn Y, nó sẽ tải xuống các gói liên quan đến Zimbra và có thể mất thời gian tùy thuộc vào tốc độ internet của bạn.

```
The system will be modified.  Continue? [N] Y
```

- Khai báo domain. Chỗ này bạn sẽ create lại tên domain. Sửa lại như hình sau:

<img src="https://i.imgur.com/MgRvDuZ.png">

- Enter để tiếp tục 

<img src="https://i.imgur.com/WtoQiJl.png">

- Chọn 7 sau đó chọn 4 để thiết lập tài khoản admin

<img src="https://i.imgur.com/rk1rRzS.png">

- Chọn r để xem lại cài đặt, chọn a để lưu thay đổi 

```
Select, or 'r' for previous menu [r] r

Main menu

   1) Common Configuration:                                                  
   2) zimbra-ldap:                             Enabled                       
   3) zimbra-logger:                           Enabled                       
   4) zimbra-mta:                              Enabled                       
   5) zimbra-dnscache:                         Enabled                       
   6) zimbra-snmp:                             Enabled                       
   7) zimbra-store:                            Enabled                       
   8) zimbra-spell:                            Enabled                       
   9) zimbra-proxy:                            Enabled                       
  10) zimbra-imapd:                            Enabled                       
  11) Default Class of Service Configuration:                                
   s) Save config to file                                                    
   x) Expand menu                                                            
   q) Quit                                    

*** CONFIGURATION COMPLETE - press 'a' to apply
Select from menu, or press 'a' to apply config (? - help) a
Save configuration data to a file? [Yes] Yes
```

- Hoàn tất việc cài đặt 

```
Save configuration data to a file? [Yes] Yes
Save config in file: [/opt/zimbra/config.27612] /opt/zimbra/config.27612
Saving config in /opt/zimbra/config.27612...done.
The system will be modified - continue? [No] Yes
............
.............
..............
Notify Zimbra of your installation? [Yes] Yes
.
.
.
Notification complete

Checking if the NG started running...done. 
Setting up zimbra crontab...done.


Moving /tmp/zmsetup.20190405-035429.log to /opt/zimbra/log


Configuration complete - press return to exit
```

- Mở firewalld

```
firewall-cmd --permanent --add-port={25,80,110,143,443,465,587,993,995,5222,5223,9071,7071}/tcp
firewall-cmd --reload
```

## Bước 4: Thiết lập các bản ghi DKIM, DMARC

- Trên VPS sử dụng user `zimbra` thực hiện lệnh sau để tạo DKIM cho domain

```
[zimbra@mail ~]$ /opt/zimbra/libexec/zmdkimkeyutil -a -d supportdao.com
DKIM Public signature:
9311B7B2-5781-11E9-8428-2F9914CF09B1._domainkey	IN	TXT	( "v=DKIM1; k=rsa; "
	  "p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAoKi5ruxutL2K+9sJ1g7ZqeaQrQHK+lKo99eEvEcpBWVNx2PHgfHJAo449i4xFMpuZO0pGeewSnxIV773FskUdoiqHVytOejniZ0cV6IrlEJiBg/t5DYRzA4YRe5GVz6Ije3zastUfxAZxxzWqQpS2jeoLUVoUR259Fc2cWamdp4bqmcX7pltL5WAsEyjr0ooxYqNAolv4dxjN/"
	  "h5oyXeVDL3K7Xk/sjbgFCJYQIkBvjv0VOcos8X6DqYAzZHUzYT8mpKKrInaYGxU9KzU3BkqNZTOFaSV7iIk0yYvKOyxzGBWcb4EwtEqz89t+InYVYCd5WQWIW8iG5ivjpPjaxSwQIDAQAB" )  ; ----- DKIM key 9311B7B2-5781-11E9-8428-2F9914CF09B1 for supportdao.com
```

- Truy cập lại trang zonedns khai báo các bản ghi còn lại

||||
|-|-|-|
| _dmarc | TXT | v=DMARC1; p=reject; rua=mailto:admin@supportdao.com |
| @ | TXT | v=spf1 mx ~all |
|9311B7B2-5781-11E9-8428-2F9914CF09B1._domainkey| TXT | Giá trị như trong ()|

- Xem cụ thể hơn ở ảnh sau:

<img src="https://i.imgur.com/dcNx18m.png">

## Bước 5: Truy cập vào side admin

- Truy cập vào địa chỉ 

```
https://mail.supportdao.com:7071
```

<img src="https://i.imgur.com/umLON80.png">

Chúc các bạn thành công 

## Bước 6: Thiết lập SSL

- Bạn có thể tham khảo từ bài viết trước trong blog của chúng tôi tại <a href="https://blog.cloud365.vn/other/cai-dat-Let-Encrypt-trong-zimbra/">đây</a>

## Kết quả

- Gửi mail từ email server của mình tới gmail 

<img src="https://i.imgur.com/EeuyBcA.png">

Chúc các bạn thành công. 