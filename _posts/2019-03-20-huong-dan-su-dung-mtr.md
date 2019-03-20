---
date: 2019-03-20
title: Hướng dẫn sử dụng công cụ MTR (My Traceroute)
categories:
  - Linux
description: Hướng dẫn sử dụng công cụ MTR (My Traceroute)
author: duydm
tags: [Beginer, Linux]
type: Document
---

Đối với một người quản trị hệ thống việc giám sát sự hoạt động ổn định của hệ thống là một điều cực ký quan trọng, các hệ thống triển khai dịch vụ online thì vấn đề được đặt lên hàng đầu đó là `network`. Để có thể giám sát được tình trạng online, các package gửi/nhận thông thường quản trị hệ thống sử dụng các command line như `ping`, `traceroute` để xem quá trình truyển tải các package trên mạng qua các điểm có ổn định hay không. Thay vì sử dụng những câu lệnh đơn lẻ và phải tập hợp các thông số rời rạc để phân tích đưa ra nhận định, bạn có thể sử dụng các tool chuyện dụng cho việc phân tích mạng. Sau đây sẽ hướng dẫn các bạn sử dụng công cỵ `mtr` (my traceroute) để thực hiện đánh giá, phân tích trạng thái của network.

![](/images/img-mtr/mtr.png)

`MTR` là một công cụ bao gồm 2 chương trình `traceroute` và `ping`. Khi bạn thực hiện chạy công cụ `mtr` nó sẽ thực hiện kết nối giữa host bạn đang cài mtr và một máy chủ khác thông qua địa chỉ IP hoặc hostname bằng cách gửi các package TTLs với chỉ số thấp qua các điểm khác nhau cho tới disk, các gói tin được gửi liên tục, % tỷ lệ được tính bằng gói tin gửi đi, gói tin gửi về từ đó giúp người quản trị đưa ra nhận định chính xác về tình trạng network kết nối tới host/server cần kiểm tra.

## 1. MTR trên Linux

### 1.1. Cài đặt

Để sử dụng được công cụ mtr trên Linux bạn thực hiện cài đặt các gói mtr trên môi trường Linux

**Host mrt CentOS**

```
yum install mtr
```

**Host mrt Ubunutu**

```
apt-get install mtr
```

### 1.1.2. Thao tác sử dụng

**Kiểm tra kết nối tới domain/IP**

```
mtr [domainName/IP]
```
![](/images/img-mtr/Screenshot_1221.png)

![](/images/img-mtr/Screenshot_1222.png)

**Hiển thị địa chỉ IP và hostname tương ứng của các điểm kết nối**

```
mtr -b [domainName/IP]
```
![](/images/img-mtr/Screenshot_1223.png)

![](/images/img-mtr/Screenshot_1224.png)













## 2. MTR trên Windows

### 2.1. Cài đặt

### 2.2. Thao tác sử dụng














---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>