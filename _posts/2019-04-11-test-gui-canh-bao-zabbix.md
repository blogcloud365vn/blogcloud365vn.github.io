---
date: 2019-04-11
title: Cấu hình test gửi cảnh báo trên zabbix
categories:
  - Monitor
description: Hướng dẫn cấu hình test gửi cảnh báo trên zabbix
author: niemdt
tags: [Zabbix]
type: Document
---

## Tổng quan

Khi bạn tạo ra một action gửi cảnh báo trên zabbix bạn không biết nó đã thành công hay chưa? Thông thường bạn sẽ phải đợi đến khi có một host nào đó có cảnh báo. Như vậy bạn phải chờ đợi xem liệu action mình vừa tạo có hoạt động hay không nhưng bạn lại không muốn nhận cảnh báo này vì chỉ khi hệ thống có sự cố thì mới có cảnh báo mà không ai muốn hệ thống của mình gặp sự cố bao giờ. Chính vì vậy trong bài viết này tôi sẽ hướng dẫn bạn cách làm thế nào để kiểm tra xem hệ thống gửi cảnh báo của mình đã hoạt động.

## Mô hình

![](/images/img-zabbix-sender/5.png)

Tôi thực hiện tạo item và trigger cho một host và sau đó truy cập vào host để gửi mooth giá trị lên zabbix server

## Cài đặt

**Tạo item**

Đầu tiên cần vào một host trên zabbix mà bạn có thể truy cập vào nó để tạo một item.

![](/images/img-zabbix-sender/1.png)

Ở đây bạn tạo item với type là `Zabbix trapper`. Key ở đây bạn đặt một key tùy ý.

Với type là zabbix trapper thì nó zabbix server có thể nhận bất kỳ giá trị nào cũng như bất kỳ thời điểm nào khi ta dùng lệnh `zabbix_sender` trên host để gửi dữ liệu đến nó.

**Tạo trigger**

Tiếp theo bạn cần tạo một trigger với item vừa tạo

![](/images/img-zabbix-sender/2.png)

Ở phần `Expression` bạn thực hiện add một item bạn vừa tạo bên trên vào 

![](/images/img-zabbix-sender/3.png)

Với trigger vừa tạo thì khi giá trị của item `zabbix-test-action` của tôi có giá trị lớn hơn 5 thì sẽ có cảnh báo

**Cài đặt zabbix-sender**

Bây giờ bạn truy cập vào host có mà bạn vừa tạo item trên đó để tiến hành cài đặt `zabbix-sender`. Để cài đặt bạn thực hiện lệnh sau:

```
yum install zabbix-sender
```

Bây giờ dùng lệnh `zabbix_sender` để tiến hành gửi giá trị lên zabbix server. Cú pháp như sau

```
zabbix_sender -z IP-server -s "name" -p port -k key -o giá-trị
```

Trong đó:
 * `IP-server` là địa chỉ của máy zabbix server
 * `name` là tên của của máy ta đang đứng được khai báo trên zabbix server.
 * `port` chỉ ra port mà trên server để kết nối tới. Nếu ko khai báo sẽ sử dụng port default là 10051
 * `key` là item key
 * `giá trị` khai báo giá trị để gửi nó lên server

Như ví dụ của tôi tôi sẽ thực hiện gửi một giá trị vượt ngưỡng cảnh báo để tôi có thể kiểm tra xem cảnh báo có được gửi hay không

```
[root@zabbix2 ~]# zabbix_sender -z 10.10.10.160 -s "Host-10.162" -p 10051 -k test.send.notified -o 6 
info from server: "processed: 1; failed: 0; total: 1; seconds spent: 0.000107"
sent: 1; skipped: 0; total: 1
```

Tôi đã thấy cảnh báo được gửi về telegram

![](/images/img-zabbix-sender/4.png)

Lưu ý: Để nhận được thông báo gửi về thì bạn có cần phải cấu hình trước đó. Để cấu hình gửi thông báo về telegram tham khảo <a href="https://blogcloud365vn.github.io/monitor/zabbix4-thiet-lap-canh-bao-qua-telegram/" target="_balnk">tại đây</a> hoặc gửi cảnh báo qua mail <a href="https://blogcloud365vn.github.io/monitor/zabbix4-thiet-lap-canh-bao-qua-email/" target="_blank">tại đây</a>.

## Tổng kết

Với cách này bạn có thể dễ dàng kiểm tra hoạt động của các action mới tạo. Trong bài viết chắc chắn còn nhiều thiếu sót rất mong được sự góp ý của các bạn.

Chúc bạn thành công!

**Tài liêu tham khảo**

http://cavaliercoder.com/blog/testing-zabbix-actions.html

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>