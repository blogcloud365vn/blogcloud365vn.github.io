---
date: 2019-04-20
title:  Zabbix 4.0 - Hiển thị dung lượng sử dụng thư mục root dạng phần trăm
categories:
  - Monitor
description: Tài liệu hướng dẫn hiển thị giá trị Used disk space on / dạng phần trăm
author: duydm
tags: [Zabbix]
type: Document
---

Khi bạn cài đặt `zabbix server` để giám giát các server chạy hệ điều hành nhân linux, mặc định <a href="https://blog.cloud365.vn/monitor/cai-dat-zabbix-4-lts-tren-centos7/" target="_blank">zabbix </a>hỗ trợ template **"Template OS Linux"** cho phép thu thập, giám sát, hiển thị các thông tin cơ bản về Memory , Filesystems, OS, Performance, Network interfaces, Processes, Security, CPU. Nhưng khi sử dụng template sẵn có cũng đáp ứng được hết các nhu cầu giám giát của người quản trị. Để có thể giám sát được các thông số theo yêu cầu bạn phải tùy biến lại một số phần về `item` trong những template sẵn có của zabbix.

Mặc định `Template OS Linux` hiển thị `Used disk space on /` (Dung lượng sử dụng thư mục root trong server linux) đơn vị dạng `GB`. Thư mục / (root) là phần chiếm dung lượng lớn của disk, để biết dung lượng disk trình trạng sử dụng thế nào bạn phải theo dõi tham số này. Khi hiển thị đơn vị dạng `GB` không cho người quản trị thấy được server của bạn disk sử dụng sắp hết hay như thế nào, chỉ khi biểu thị dưới dạng `%` thì bạn có thể hiểu rõ dung lượng disk sắp hết hay vẫn còn có thể sử dụng.

![](/images/img-zabbix-used-disk-phan-tram/Screenshot_1302.png)

Sau đây sẽ hướng dẫn bạn hiển thị giá trị Used disk space on / dạng phần trăm trên zabbix server khi monitor server linux.

## 1. Truy cập zabbix server

Truy cập địa chỉ `http://ip-zabbix-server/zabbix/` để login vào zabbix server.

![](/images/img-zabbix-used-disk-phan-tram/Screenshot_1303.png)


## 2. Tạo item 	Used disk space on / (percentage)

Lựa chọn template `Template OS Linux`.

Click `Configuration -> Templates -> Template OS Linux`

![](/images/img-zabbix-used-disk-phan-tram/Screenshot_1304.png)


Tạo item `Used disk space on / (percentage)`

Click `Items -> Create item`

![](/images/img-zabbix-used-disk-phan-tram/Screenshot_1305.png)

Nhập các thông tin cho item mới

```
Name: Used disk space on / (percentage)
Type: Caculated
Key: use.disk.root
Formula: 100*last("vfs.fs.size[/,used]")/last("vfs.fs.size[/,total]")
Type of information: Numneric(float)
Unit: %
Update interval: 5m
Applications: Filesystems
```
**Name**: Tên gợi nhớ cho item.<br>
**Type**: Lựa chọn kiểu tùy biến item.<br>
**Key**: Tùy đặt 1 item chỉ có 1 key duy nhất.<br>
**Formula**: Các biểu thức tính toán để lấy giá trị tương ứng với type `Caculated`.<br>
**Type of information**: Kiểu đơn vị hiển thị.<br>
**Unit**: Đơn vị hiển thị.
**Update interval**: Khoảng thời gian zabbix-server cập nhật lại metric.<br>
**Applications**: Lựa chọn phân loại thư mục cho item mới.

![](/images/img-zabbix-used-disk-phan-tram/Screenshot_1306.png)

Click `Add`

![](/images/img-zabbix-used-disk-phan-tram/Screenshot_1307.png)

Items mới **Used disk space on / (percentage)** được tạo.

![](/images/img-zabbix-used-disk-phan-tram/Screenshot_1308.png)

## 3. Hiển thị giá trị Used disk space on / (percentage)

Click `Monitoring -> Lastest data -> Lựa chọn Host`

![](/images/img-zabbix-used-disk-phan-tram/Screenshot_1309.png)

Với hướng dẫn cơ bản trên giúp bạn giám sát thêm được thông tin về disk trông server linux.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>