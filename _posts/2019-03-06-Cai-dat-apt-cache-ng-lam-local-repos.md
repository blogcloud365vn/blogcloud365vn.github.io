---
title: Cài đặt apt-cache-ng làm local repo.
categories:
  - Linux
description: Cài đặt apt-cache-ng làm local repo.
author: datpt
tags: [Linux]
type: Document
---

## Lời mở đầu.

Apt-Cacher-NG là một caching proxy server, sau khi cài đặt, nó sẽ "cache" các gói đã download từ trên internet về và đặt tại server cho những lần sử dụng sau. Nhờ vậy, các máy khác lần sau sẽ không cần phải mất thời gian tải các packages từ trên internet về nữa.

Hoạt động theo mô hình client server.

Với apt-cacher-ng, chúng ta có thể theo dõi sự update và download các gói phần mềm được thiết lập và hỗ trợ sử dụng trong cả các môi trường.

Các tính năng:
    - Tiết kiệm thời gian.
    - Tiết kiệm băng thông.
    - Người dùng có thể tích hợp dữ liệu từ file ISO hoặc DVD vào apt-cacher-ng bằng cách sử dụng tùy chọn "import".

## Mô hình cài đặt.

![mohinh](/images/img-apt-cache-ng/mohinh.png)

## Cài đặt trên Ubuntu 16.04.

Cài đặt apache2:

```sh
apt-get -y install apache2
```


Cài đặt apt-cacher-ng bằng câu lệnh:

```sh
sudo apt-get install apt-cacher-ng
```

Mở file cấu hình:

```sh
vi /etc/apt-cacher-ng/acng.conf
```

Tại dòng 36 bỏ comment để mở port 3142:

```sh
Port: 3142
```

Tại dòng 48, sửa lại như sau:

```sh
BindAddress: 0.0.0.0
```

Tại dòng 61, thêm cấu hình sau:

```sh
Remap-centos: file:centos_mirrors /centos
```

Tại dòng 101, bỏ comment của thông số sau:

```sh
VerboseLog: 1
```

Tại dòng 110, bỏ comment tại:

```sh
PidFile: /var/run/apt-cacher-ng/pid
```

Tại dòng 162 sửa lại giá trị như sau:

```sh
DnsCacheSeconds: 2000
```

Thêm 2 dòng cấu hình sau vào cuối file:

```sh
VfilePatternEx: ^/\?release=[0-9]+&arch=
VfilePatternEx: ^(/\?release=[0-9]+&arch=.*|.*/RPM-GPG-KEY-examplevendor)$
```

Sau đó lưu lại cấu hình và tiến hành tạo `mirror list` bằng câu lệnh sau:

```sh
curl https://www.centos.org/download/full-mirrorlist.csv | sed 's/^.*"http:/http:/' | sed 's/".*$//' | grep ^http >/etc/apt-cacher-ng/centos_mirrors
```

Mở file cấu hình tại dòng 406 thêm dòng cấu hình sau:

```sh
PassThroughPattern: .*
```

Restart lại dịch vụ :

```sh
service apt-cacher-ng restart
```

Truy cập vào trang quản trị thông qua port 3142:

```sh
ip-server:3142
```

Giao diện như sau:

![web](/images/img-apt-cache-ng/web.png)

Trên máy client chúng ta thêm các cấu hình để  khai báo repo:

Với client là Ubuntu :

```sh
echo 'Acquire::http { Proxy "http://x.x.x.x:3142"; };' > /etc/apt/apt.conf.d/01proxy
```

Với client là Centos:

```sh
echo "proxy=http://x.x.x.x:3142" >> /etc/yum.conf
```

## Tổng kết.

Như vậy ở bài này mình đã hướng dẫn xong cách cài đặt apt-cache-ng để làm local repo, chúc mọi người thành công.



---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>