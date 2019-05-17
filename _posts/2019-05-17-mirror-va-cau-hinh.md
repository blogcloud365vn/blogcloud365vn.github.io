---
date: 2019-05-17
title: Giới thiệu và cấu hình quản lý gói
categories:
  - Linux
description: Giới thiệu và cấu hình quản lý gói
author: tuanda
tags: [mirrors, linux, CentOS7, CentOS6, CentOS]
type: Document
---

## Giới thiệu

Hầu hết các hệ điều hành giống Unix hiện đại đều cung cấp cơ chế tập trung cho việc tìm kiếm và cài đặt phần mềm. Phần mềm thường được phân phối dưới dạng gói (package), giữ trong kho lưu trữ (repo).

Trong Debian và các hệ thống dựa trên nó, như Ubuntu, Linux Mint và Raspbian, định dạng gói là `.deb` . APT - Advanced Packaging Tool, cung cấp các lệnh được sử dụng cho các hoạt động phổ biến : Tìm kiếm, cài đặt,..

CentOS, Fedora và các thành viên khác của gia đình Red Hat sử dụng các tệp RPM. Trong CentOS, yum được sử dụng để tương tác với cả tệp gói và kho lưu trữ

## Yum Repository

Là kho tập hợp các phần mềm Linux (các gói `.rpm`).

Việc khai báo Repo được thực hiện bằng khối `[repository]` trong file `/etc/yum.conf` hoặc định nghĩa bằng file có đuôi `.repo` trong thư mục : `/etc/yum.repos.d/`

- Ví dụ, Repo của MariaDB:

```sh
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.2/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

## Mirrors

Mirrors cung cấp các repo cho các OS. Có thể local mirror hoặc đăng ký trở thành Official Mirrors (Ubuntu, CentOS, Debian,..)

Ví dụ như : mirror.centos.org, mirrors.nhanhoa.com,..

## Yum plugins

Các plugin sử dụng trong YUM, trong đó có plugin `fastestmirror` để xác định mirror có thời gian phản hồi tốt nhất.

File cấu hình nằm trên: `/etc/yum/pluginconf.d/fastestmirror.conf`

Khi được cấu hình  `enabled=1` trong `[main]`, yum sẽ dựa vào thời gian phản hồi lưu ở cache để tìm đến mirror. File cache nằm ở : `/var/cache/yum/x86_64/6/timedhosts.txt `

## Cấu hình sử dụng mirror Nhân Hòa trên CentOS 6:

```sh
sed -i "s/enabled=1/enabled=0/g" /etc/yum/pluginconf.d/fastestmirror.conf

curl -o /etc/yum.repos.d/nhanhoa.repo https://raw.githubusercontent.com/anhtuan204/ghichep/master/TuanDA/script/nhanhoa-centos6.repo
```

Sau đó thực hiện cài đặt các gói và kiểm chứng:

![](/images/img-mirrors/mirror_1.png)

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>