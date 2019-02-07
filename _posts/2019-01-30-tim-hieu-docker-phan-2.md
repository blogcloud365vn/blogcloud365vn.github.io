---
title: "Tìm hiểu về Docker - Phần 2 - Cài đặt Docker trên CentOS 7"
categories:
  - Container
description: Tìm hiểu về Docker - Phần 2 - Cài đặt Docker trên CentOS 7
author: huytm
tags: [Docker, Centos]
type: Document
---

## Mở đầu

Ở phần [trước](https://blog.cloud365.vn/container/tim-hieu-docker-phan-1/){:target="_blank"} mình đã giới thiệu tổng quan về container và Docker. Phần này mình sẽ viết tiếp về cách cài đặt Docker trên CentOS 7.

Phiên bản cài đặt mình sử dụng là Docker CE. Để phân biệt Docker CE và Docker EE thì các bạn tham khảo thêm tại [đây](https://blog.docker.com/2017/03/docker-enterprise-edition/){:target="_blank"} nhé.

## 1. Chuẩn bị

Một [SSD Cloud VPS](https://cloud365.vn){:target="_blank"}  CentOS7 có cấu hình như sau:

<p align="center">
<img src="/images/img-docker/docker2/r.png">
</p>

## 2. Cài đặt Docker sử dụng yum và repository

**Cài đặt các gói cần thiết**

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

**Thêm Docker repo**

```
sudo yum-config-manager --add-repo https://download.docker.com/li 
```

**Cài đặt bản lastest của Docker CE**

```
sudo yum install docker-ce docker-ce-cli containerd.io
```

**Kiểm tra lại cài đặt**

```
sudo systemctl start docker
docker -v
```

*Docker version 18.09.1, build 4c52b90*

**Chạy container đầu tiên với Docker**

```
sudo docker run hello-world
```

<p align="center">
<img width="600" height="400" src="/images/img-docker/docker2/docker-done.png">
</p>


Bản chất của câu lệnh trên, Docker sẽ pull một image là **hello-world** trên **Docker hub** về server và chạy container với image đó.

## Tổng kết

Trong bài này mình đã viết cách cài đặt Docker trên CentOS 7. Hy vọng sẽ giúp các bạn cài đặt thành công trong quá trình tìm hiểu Docker. Ở phần tiếp theo mình sẽ viết về một số câu lệnh CLI để quản lý **image** và **container**

>"if you have knowledge let others light their candles in it"

---

### Tài liệu tham khảo
[https://blog.docker.com/2017/03/docker-enterprise-edition/](https://blog.docker.com/2017/03/docker-enterprise-edition/){:target="_blank"}


---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>

