---
title: Hướng dẫn cấu hình và sử dụng iLO server HP
categories:
  - Linux, Beginner
description: Tài liệu Hướng dẫn cấu hình iLO và thao tác giám sát server HP
author: duydm
tags: [Beginner, Linux]
type: Document
---

### Mục lục

[1, Tìm hiểu về iLO](#timhieuveilo)

[2, Cài đặt iLO](#caidatilo)

<a name="timhieuveilo"></a>
## 1, Tìm hiểu về iLO ##

Đối với dòng server HP thế hệ mới iLO (intergrated Lights Out) cung cấp tính năng để quản lý các thông số hardware của server từ xa, troubleshoot, remote thông qua một giao diện. iLO là một cổng riêng biệt trên server, có chương trình và phần cứng độc lập, giúp quản lý phần cứng server độc lập, nó có chương trình WebGUI để quản lý cũng như command line tương ứng.

 iLO được cấu hình thông qua một port riêng, kết nối qua đường RJ45 được cấu hình các thông số về network.

![](/images/img-ilo-hp/ilo1.jpg)

<a name="caidatilo"></a>
## 2, Cài đặt iLO ##

Khởi động server HP để server chạy khi thấy dòng Config iLO thì ấn phím chức năng tương ứng F8

![](/images/img-ilo-hp/ilo2.jpg)

+ Cấu hình network cho port iLO

Tắt chế độ DHCP

Network -> DNS/DHCP

![](/images/img-ilo-hp/ilo3.jpg)

Network -> NIC and TCP/IP

![](/images/img-ilo-hp/ilo4.jpg)

Nhập các thông số về địa chỉ IP

![](/images/img-ilo-hp/ilo5.jpg)

+ Cấu hình password cho user

Change pass used administrator

User -> Nhập password mới -> F10 để save lại

![](/images/img-ilo-hp/ilo6.jpg)

+ Thoát ra và khởi động lại máy

+ Login vào iLO qua địa chỉ IP và tài khoản đãn cấu hình

https://ip_iLO/

![](/images/img-ilo-hp/ilo7.png)
Login thành công

![](/images/img-ilo-hp/ilo8.png)

## 3.Giám sát các thông tin trên iLO ##

**3.1. Tab information**

Overview: Hiển thị tổng quan về server firmware, trạng thái tắt bật...

+ System Information: Trạng thái, thông tin về các bộ phận của server

![](/images/img-ilo-hp/ilo9.png)

+ iLO event log: Ghi lại nhật kí về việc cài đặt thông tin cho iLO

![](/images/img-ilo-hp/ilo10.png)

+ Integrated Management Log: Thông tin nhật ký vận hành phần cứng server

![](/images/img-ilo-hp/ilo11.png)

+ 
**3.2. Tab Remote console**

Cho phép người quản thị điều khiển màn hình console của server từ xa

![](/images/img-ilo-hp/ilo12.png)

**3.3. Tab Virtual Media**

Cung cấp chức nắng đính kèm file ISO phục vụ cài đặt OS từ xa và chỉnh sửa thứ tự boot của server

![](/images/img-ilo-hp/ilo13.png)

**3.4. Power Management**

Chức năng quản lý việc bật/tắt server từ xa

![](/images/img-ilo-hp/ilo14.png)

**3.5. Administration**

Chức năng quản lý thông tin về iLO như license, firmware, cài đặt ip, user...

+ iLO Firmware: Hiển thị firmware iLO

![](/images/img-ilo-hp/ilo15.png)

+ User Administration: Thiết lập tài khoản login iLO

![](/images/img-ilo-hp/ilo16.png)

+ Access Settings: Cài đặt kiểm soát truy cập tới các port của server

![](/images/img-ilo-hp/ilo17.png)

+ Security: Thiết lập login tới iLO

![](/images/img-ilo-hp/ilo18.png)

+ Network: Thiết lập thông tin IP cho port iLO

![](/images/img-ilo-hp/ilo19.png)

Xin chân thành cảm ơn!