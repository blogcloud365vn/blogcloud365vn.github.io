---
date: 2019-03-26
title: Gửi cảnh báo tới telegram sử dụng python
categories:
  - Linux
description: Gửi cảnh báo tới telegram sử dụng python
author: huytm
tags: [Python, Linux]
type: Document
---

## Mở đầu

Bạn là một SysAd, và bạn thường xuyên phải theo dõi, giám sát hệ thống của mình. Bạn đã sử dụng nhiều tool giám sát như Zabbix, hay CheckMK. Bạn phải viết thêm plugin cho chúng, và gửi mail tự động mỗi khi có cảnh báo ? 
Tuy nhiên với một hệ thống đủ lớn, hoặc đủ để phải chăm chút thường xuyên thì một kênh cảnh báo là chưa đủ, cảnh báo đa kênh mới là điều cần thiết.
Thật may, giờ đây với một thế giới mở, với các API và thư viện đầy rẫy thì việc tích hợp giữa các ứng dụng với nhau đã trở nên dễ dàng hơn.
Trong phạm vi bài viết này mình sẽ giới thiệu một kênh thông tin nữa để các bạn có thể sử dụng để nhận cảnh báo đó là `Telegram`. 

## Tạo Telegram bot

**Bước 1**: Chat với BotFather để tạo một bot

- Tại ô **Search** của Telegram chọn BotFather 
- Chat `/newbot` vào khung chat với BotFather
- Nhập tên cho bot. Ở đây mình chọn là **test_bot**
- Chọn username cho bot. Ở đây mình chọn là `TenCuaBot`

Quá trình tạo bot diễn ra như sau:

<p align="center">
<img src="/images/img-telegram-python/anh1.png">
</p>

Sau khi hoàn tất bạn sẽ nhận được 1 token, và ở ví dụ này của mình sẽ là:  `Part00000:Part1111111111111111-Part22222222222222`

**Bước 2**: Thêm bot vào group:

- Chọn group cần thêm
- Chọn *Add member* → *@your_ot*. Ở ví dụ này của mình sẽ là *@test_bot*
- Khởi động bot bằng cách chat với bot trong room. Ở ví dụ này của mình sẽ là `/my_id @TenCuaBot`

**Bước 3**: Lấy chat_id

Để lấy được chat_id bạn truy cập vào url sau:

`https://api.telegram.org/bot[TOKEN]/getUpdates`

> Ví dụ https://api.telegram.org/botPart00000:Part1111111111111111-Part22222222222222/getUpdates

Tại reponse trả về kiểu json, bạn chú ý đến key có tên là result.chat.id có id bắt đầu bằng dấu `-` đó chính là chat_id của bạn. Hãy note lại **token** có được ở Bước 1 và **chat_id** này nhé. Ở ví dụ này chat_id của mình là *-123456789123*

## Gửi cảnh báo tới Telegram qua API

Sử dụng luôn chính trình duyệt của bạn với method *GET* của http với cú pháp sau:

```
https://api.telegram.org/bot[TOKEN]/sendMessage?chat_id=[CHAT_ID]&text=[MY_MESSAGE_TEXT]
```

## Gửi cảnh báo sử dụng curl

Cú pháp

```
curl -X POST "https://api.telegram.org/bot[TOKEN]/sendMessage" -d "chat_id=[CHAT_ID]&text=[MY_MESSAGE_TEXT]"
```

## Gửi cảnh báo sử dụng thư viện telegram của python

Hai cách ở trên mình sử dụng để mình kiểm tra hoạt động của bot mà thôi. 
Thật ra các bạn cũng có thể sử dụng thêm các công cụ khác như postman, JMeter... Sử dụng bash/shell, php, javascript ... là tùy ở bạn. Nhưng như đã nói từ đầu, trong phạm vi bài viết này mình sẽ sử dụng python.
Server của mình là [SSD Cloud VPS](https://cloud365.vn){:target="_blank"}  CentOS7

**1. Cài đặt Python**

Cài đặt Python 3.6 các bạn có thể tham khảo bài viết này [Cài đặt Python](https://blog.cloud365.vn/linux/other/huong-dan-cai-dat-python36-tren-centos7/){:target="_blank"}

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
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

**Cài đặt bản lastest của Docker CE**

```
sudo yum install -y docker-ce docker-ce-cli containerd.io
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
