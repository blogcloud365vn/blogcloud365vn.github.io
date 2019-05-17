---
date: 2019-05-17
title: Hướng dẫn đăng nhập zabbix khi quên mật khẩu Admin
categories:
  - Monitor
description: Hướng dẫn đăng nhập zabbix khi quên mật khẩu Admin
author: tuanda
tags: [mirrors, linux, CentOS7, CentOS6]
type: Document
---

## Yum

Yellowdog Updater, Modified (viết tắt là yum) là một trình quản lý gói tin dòng lệnh mã nguồn mở cho các máy tính sử dụng hệ điều hành Linux dùng RPM Package Manager.

## Yum Repository

Là kho tập hợp các phần mềm Linux (các gói `.rpm`).

Việc khai báo Repo được thực hiện bằng khối `[repository]` trong file `/etc/yum.conf` hoặc định nghĩa bằng file có đuôi `.repo`trong thư mục : `/etc/yum.repos.d/`

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

## Yum plugin

Các plugin sử dụng trong YUM, trong đó có plugin `fastestmirror` để xác định mirror có thời gian phản hồi tốt nhất.

File cấu hình nằm trên: `/etc/yum/pluginconf.d/fastestmirror.conf`

Khi được cấu hình  `enabled=1` trong `[main]`, yum sẽ dựa vào thời gian phản hồi lưu ở cache để tìm đến mirror. File cache nằm ở : `/var/cache/yum/x86_64/6/timedhosts.txt `

## Cấu hình sử dụng mirror Nhân Hòa trên CentOS 6:

sed -i "s/enabled=1/enabled=0/g" /etc/yum/pluginconf.d/fastestmirror.conf



Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>