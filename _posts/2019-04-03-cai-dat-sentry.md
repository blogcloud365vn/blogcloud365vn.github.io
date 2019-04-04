---
date: 2019-04-04
title: Hướng dẫn cài đặt sentry trên centOS 7
categories:
  - Linux
description: Hướng dẫn cài đặt sentry trên centOS 7
author: huytm
tags: [Python, Linux]
type: Document
---

## Mở đầu

<p align="center">
<img src="/images/img-sentry/img-sentry0.png">
</p>

Bạn đang là Developer và bạn cần tracking các exceptions trong ứng dụng của mình ? Bạn đã bắt log cho ứng dụng của mình nhưng vẫn bị sót các exception? Hoặc đơn giản bạn muốn đếm số lần xuất hiện một lỗi cụ thể ở ứng dụng của bạn.
Có rất nhiều công cụ có thể làm được việc đếm log, thống kê log lỗi ví dụ ELK, Graylog ... Nhưng với môi trường cụ thể là Dev thì các công cụ trên có thể sẽ chưa phù hợp cho lắm.
Và ở bài viết này mình sẽ hướng dẫn các bạn cài đặt và sử dụng **Sentry**, một công cụ tích hợp để quản lý lỗi trong ứng dụng của bạn.

## Sentry là gì ?

[Sentry](https://sentry.io/){:target="_blank"} là một giải pháp *open-source* cung cấp một nền tảng có khả năng *realtime* tracking và logging. Sentry kiểm soát lỗi và hiển thị cho bạn biết khi nào có lỗi, lỗi xảy ra ở đâu, user nào đang bị dính lỗi.
Đặc biệt Sentry hỗ trợ khá nhiều ngôn ngữ và framework trong đó có Ruby, Js, Java, Django, iOS, .NET ...

<p align="center">
<img src="/images/img-sentry/img-sentry1.png">
</p>

## Tại sao mình lại lựa chọn sử dụng Sentry

- Xem log lỗi, các exceptions realtime
- Kiểm soát được user nào đang gặp lỗi
- Kiểm soát được số lần thất bại. Điều này giúp ngăn chặn được những hành vi không hợp pháp đối với ứng dụng của bạn
- Có khả năng tích hợp với các công cụ khác như GitLab, GitHub, Hipchat, Heroku ...
- Hệ thống gửi cảnh báo linh hoạt và nhiều.
- Có báo cáo thống kê theo tuần, tháng ...


## Cài đặt Sentry

### CÁCH 1: CÀI MANUAL TRÊN CENTOS 7

Chuẩn bị một [SSD Cloud VPS](https://cloud365.vn){:target="_blank"} CentOS7 có cấu hình tối thiểu như sau: 

<p align="center">
<img src="/images/img-jekyll/image1.png">
</p>

#### Bước 1. Chuẩn bị

Giả vờ môi trường là lý tưởng, disable SELinux, và stop firewall và cài đặt các gói cần thiết

- Tắt firewall, SELinux

```bash
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld
reboot
```

- Sentry yêu cầu python 2.7. Kiểm tra version của python.

```bash
python --version
```

- Cài đặt epel và update

```
yum install epel-release -y
yum update -y
```

- Cài đặt các gói cần thiết

```
yum install python-pip mailcap -y
yum -y install gcc gcc-c++ kernel-devel
yum -y install python-devel libxslt-devel libffi-devel openssl-devel
```

#### Bước 2. Cài đặt Postgresql

- Cài đặt postgres

```
yum install postgresql-server.x86_64 postgresql-contrib -y
```

- Khởi tạo DB

```
postgresql-setup initdb
```

- Start postgres

```
systemctl enable postgresql.service
systemctl start postgresql.service
```

- Sửa lại cấu hình trong file `/var/lib/pgsql/data/pg_hba.conf`

```
# "local" is for Unix domain socket connections only
local all all peer
# IPv4 local connections:
host all all 127.0.0.1/32 md5
# IPv6 local connections:
host all all ::1/128 md5
```

- Khởi động lại dịch vụ

```
systemctl restart postgresql.service
```





## Tổng kết


>"if you have knowledge let others light their candles in it"

---

### Tài liệu tham khảo


---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
