---
date: 2019-05-17
title:  Zabbix 4.0 - Hiển thị dung lượng RAM đã sử dụng dạng phần trăm
categories:
  - Monitor
description: Tài liệu hướng dẫn hiển thị giá trị RAM used dạng phần trăm trên server Linux.
author: duydm
tags: [Zabbix]
type: Document
---

Hệ điều hành nhân linux đang được sử dụng rất phổ biến, nhu cầu giám sát tài nguyên của server như RAM, Disk, CPU, Network.. được người quản trị hệ thống đặc biệt quan tâm. Khi sử dụng <a href="https://blog.cloud365.vn/monitor/cai-dat-zabbix-4-lts-tren-centos7/" target="_blank">zabbix </a> để giám sát server Linux của mình mặc định template **"Template OS Linux"** cho phép ta giám sát thông tin về **Memory** bao gồm: `Available memory`,  `Free swap space`, `Free swap space in %`, `Total memory`, `Total swap space`. Với những thông tin giám giát về memory mặc định zabbix hỗ trợ không thể cung cấp thông tin đầy đủ và trực quan nhất cho người quản trị vì trong cơ chế hoạt động về RAM của hệ điều hành Linux sẽ xuất hiện dung lượng cache cắt từ lượng RAM thực tế dẫn tới việc zabbix không thể hiển thị chính xác nhất giá trị RAM sử dụng thực tế.

Ở bài hướng dẫn này sẽ hướng dẫn hiển thị dưng lượng RAM đã sử dụng dạng phần trăm (đã trừ đi dung lượng cache) trên server Linux.

## 1. Truy cập zabbix server

Truy cập địa chỉ `http://ip-zabbix-server/zabbix/` để login vào zabbix server.

![](/images/img-ram-phan-tram/Screenshot_1482.png)

## 2. Tạo item 	RAM USED % Current

Lựa chọn template `Template OS Linux`.

Click `Configuration -> Templates -> Template OS Linux`

![](/images/img-ram-phan-tram/Screenshot_1483.png)

- Tạo item `RAM cache B`: Tính giá trị dung lượng RAM cache đơn vị là Byte

Click `Items -> Create item`

![](/images/img-ram-phan-tram/Screenshot_1484.png)

Nhập các thông tin cho item.

```
Name: RAM cache B
Type: Zabbix agent
Key: vm.memory.size[cached]
Type of information: Numneric(usigned)
Unit: B
Update interval: 1m
Applications: Memory
```

![](/images/img-ram-phan-tram/Screenshot_1486.png)

Click `Add`

![](/images/img-ram-phan-tram/Screenshot_1487.png)

- Tạo item `RAM cache %` hiển thị dung lượng RAM cache dạng %.

Ở trong giao diện template `Template OS Linux` tạo item mới và nhập các thông tin.

```
Name: RAM cache %
Type: Caculated
Key: vm.memory.pcache
Formula: 100*last("vm.memory.size[cached]")/last("vm.memory.size[total]")
Type of information: Numneric(float)
Unit: %
Update interval: 1m
Applications: Memory
```

![](/images/img-ram-phan-tram/Screenshot_1488.png)

- Tạo item `RAM pused` tính dung lượng RAM sử dụng dang phần trăm (có bao gồm cache).

Ở trong giao diện template `Template OS Linux` tạo item mới và nhập các thông tin.

```
Name: RAM pused
Type: Zabbix agent
Key: vm.memory.size[pused]
Type of information: Numneric(float)
Unit: %
Update interval: 1m
Applications: Memory
```

![](/images/img-ram-phan-tram/Screenshot_1489.png)

- Tạo item `RAM USED % Current` tính dung lượng RAM sử dụng thực tế đã trừ đi giá trị `cache`.

Tại giao diện template `Template OS Linux` tạo item mới và nhập các thông tin sau.

```
Name: RAM USED % Current
Type: Caculated
Key: vm.memory.pusednocache
Formula: last("vm.memory.size[pused]")-last("vm.memory.pcache")
Type of information: Numneric(float)
Unit: %
Update interval: 1m
Applications: Memory
```

![](/images/img-ram-phan-tram/Screenshot_1490.png)

Item mới được tạo.

![](/images/img-ram-phan-tram/Screenshot_1491.png)


## 3. Kiểm tra giá trị RAM USED % Current

Click `Monitoring -> Lastest data -> Lựa chọn Host`

![](/images/img-ram-phan-tram/Screenshot_1493.png)

Với hướng dẫn cơ bản trên giúp bạn giám sát thêm được thông tin về memory trông server linux.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>