---
date: 2019-05-06
title: Hướng dẫn sử dụng discovery network zabbix
categories:
  - Monitor
description: Hướng dẫn sử dụng discovery network để tìm kiếm IP đang hoạt động và add host tự động để giám sát trong zabbix.
author: niemdt
tags: [Zabbix]
type: Document
---

## Tổng quan

Bạn đang sử dụng zabbix để giám sát hệ thống của mình và bạn có một dải địa chỉ IP được cấp. Các thiết bị của bạn sẽ nhận địa chỉ IP trong dải này. Bạn muốn giám sát các thiết bị này một các tự động khi nó nhận một địa chỉ trong dải IP của bạn mà bạn không cần phải add từng thiết bị một và add các item và trigger để cảnh báo cho từng con. Trong bài viết này tôi sẽ sử dụng discovery trong zabbix để thực hiện việc này một cách tự động.

## Mô tả cấu hình

Trong bài lab này tôi sẽ tiến hành discovery dải mạng `10.10.10.160` đến `10.10.10.165` xem những IP nào đang hoạt động. Nếu nó hoạt động thì tôi sẽ tiến hành add nó vào group `Group discovery` để monitor. Trong group `Group discovery` tôi add template `Template Module ICMP Ping` để tiến hành giám sát theo các thông số trong template naỳ.

## Cài đặt

**Tạo discovery rule**

Click `Configuration` -> `Discovery` -> `Create discovery rule` 

![](/images/img-discovery-network-zabbix/1.png)

Ta thấy như sau:

![](/images/img-discovery-network-zabbix/2.png)

Trong đó:

`Name`: Tên của discovery rule
`Discovery by proxy`: Chọn proxy tương ứng nếu giám sát qua proxy hoặc để `No proxy` nếu giám sát trực tiếp thông qua zabbix server
`IP range`: Dải IP muốn discovery. Như ví dụ trên tôi discovery các IP từ `10.10.10.160` đến `10.10.10.165`
`Update interval`: Khoảng thời gian nghỉ giữa 2 lần discovery. Như ví trên cứ sau 30s sẽ tiến hành discovery một lần.
`Checks`: Sử dụng check qua các giao thức. Như ở đây tôi sử dụng qua giao thức ICMP ping.
`Device uniqueness criteria`: Các tiêu chí để check thiết bị. Như ở đây chỉ quan tâm đến IP.

Sau khi tạo xong click `Add`

![](/images/img-discovery-network-zabbix/3.png)

**Tạo host group**

Click `Configuration` -> `Host group` -> `Create host group`

![](/images/img-discovery-network-zabbix/4.png)

Điền tên group muốn tạo sau đó click `Add`

![](/images/img-discovery-network-zabbix/5.png)

Ta thấy đã có group

![](/images/img-discovery-network-zabbix/6.png)

**Add templates cho group**

Tạo group vừa tạo click vào `Templates`

![](/images/img-discovery-network-zabbix/7.png)

Tìm đến group muốn add sau đó click vào templates đó. Ở đây tôi add `Template Module ICMP Ping` 

![](/images/img-discovery-network-zabbix/8.png)

Tại mục `Group` ta đã thấy có một số group đã sử dụng template này. Để add template này cho một group nữa ta chọn `Select` 

![](/images/img-discovery-network-zabbix/9.png)

Chọn group ta vừa tạo và click `Select`

![](/images/img-discovery-network-zabbix/10.png)

Sau đó click `Update`

![](/images/img-discovery-network-zabbix/11.png)

Ta đã thấy Template đã được add

![](/images/img-discovery-network-zabbix/12.png)

**Tạo action**

Ta tạo action để add các host discovery được vào group ta vừa tạo để thuận lợi cho việc giám sát. 
Click `Configuration` -> `Action` sau đó chuyển `Event source` thành `Discovery` sau đó chọn `Action`

![](/images/img-discovery-network-zabbix/13.png)

![](/images/img-discovery-network-zabbix/14.png)

Trong đó:

`Name`: Tên của Action
`Type of caculation`: Nếu bạn có nhiều hơn 1 điều kiện ở trường `Conditions` ở bên dưới thì nó sẽ xuất hiện trường này.
`Conditions` Các điều kiện để action này sẽ được thực thi. Như ví du trên tôi tiến hành add các điều kiện: 
 * Trạng thái discovery trên IP đó là Up
 * Các địa chỉ được add Action phải là địa chỉ mà thuộc dải IP `Check Network` mà ta tạo bên trên.
 * Thời gian uptime lớn hơn 60s
Như vậy những IP nào thỏa mãn tất cả các điều kiện bên trên thì sẽ được thực thi các action.

Sau đó chuyển sang tab `Operations` để tạo các hành động cho IP thỏa mãn các điều kiện ta vừa tạo

![](/images/img-discovery-network-zabbix/15.png)

Bạn tạo một `Operation` mới

* Operation type: Chọn hành động bạn muốn thực hiện. Ở đây tôi chọn `Add to host group` để add những máy thỏa mãn những điều kiện bên trên vào một group
* Host groups: Chọn một group để add các host vào.

Sau đó tiến hành click lần lượt 2 nút `Add` từ trên xuống.

Ta thấy action đã được tạo

![](/images/img-discovery-network-zabbix/16.png)

Bạn kiểm tra những IP máy được discovery thấy

![](/images/img-discovery-network-zabbix/17.png)

Bây giờ tôi thử shutdown máy có địa chỉ `10.10.10.162` ta sẽ thấy sẽ thấy zabbix cảnh báo

![](/images/img-discovery-network-zabbix/18.png)

Như vậy ta thấy các host được discovery thấy đã được add vào group để giám sát các thiết bị này.

## Tổng kết

Tôi thấy cách này khá hữu ích cho việc quản lý và giám sát các thiết bị trong một dải IP. Nó sẽ thực hiện một cách tự động thay vì khi mỗi host được thêm vào dải IP này ta phải add host trên zabbix server để giám sát. Trong bài viết chắc chắn còn nhiều thiếu sót rất mong được sự góp ý của các bạn.

Chúc bạn thành công!

**Tài liệu tham khảo**

https://www.zabbix.com/documentation/4.0/manual/discovery/network_discovery/rule

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>