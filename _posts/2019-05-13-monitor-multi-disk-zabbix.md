---
date: 2019-05-13
title: Hướng dẫn monitor nhiều disk trên zabbix
categories:
  - Monitor
description: Hướng dẫn discovery disk và sử dụng plugin để giám sát nhiều disk trên zabbix
author: niemdt
tags: [Zabbix]
type: Document
---
Bạn giám sát hệ thống của mình với Zabbix. Trên server của bạn có nhiều disk và bạn muốn giám sát từng disk. Trong bài viết này tôi sẽ hướng dẫn cách sử dụng discovery rule để thực hiện việc discovery các disk và giám sát nó.

## Thực hiện trên zabbix agent

Sử dụng một script python để list ra tên disk.

```
vi /usr/local/bin/discover_disk.py
```

Sau thêm các dòng sau vào file 

```
#!/usr/bin/python
import os
import json

if __name__ == "__main__":
    # Iterate over all block devices, but ignore them if they are in the
    # skippable set
    skippable = ("sr", "loop", "ram")
    devices = (device for device in os.listdir("/sys/class/block")
               if not any(ignore in device for ignore in skippable))
    data = [{"{#DISK}": device} for device in devices]
    print(json.dumps({"data": data}, indent=4))
```

Cấp quyền thực thi cho file

```
chmod +x /usr/local/bin/discover_disk.py
```

Thêm dòng sau vào cuối file `/etc/zabbix/zabbix_agent.conf`

```
UserParameter=custom.disks.discovery_python,/usr/local/bin/discover_disk.py
```

Restart zabbix-agent

```
systemctl restart zabbix-agent
```

## Thực hiện trên dashboard

Bây giờ ta tiến hành thao tác trên dashboard.

Tạo tham số macros

![](/images/img-multi-disk-zabbix/1.png)

Tạo `discovery rule` trên host

Chọn `Configuration` -> `Host` chọn `Discovery` trên host mà ta muốn giám sát

![](/images/img-multi-disk-zabbix/2.png)

Chọn `Create discovery rule`

![](/images/img-multi-disk-zabbix/3.png)

![](/images/img-multi-disk-zabbix/4.png)

Trong đó:

 * Name: là tên của discovery rule
 * Key: Ta đặt theo parameter mà ta khai báo trong file config của zabbix agent

Sau đó chuyển sang tab `Filters` để lọc ra những disk mà ta muốn giám sát

![](/images/img-multi-disk-zabbix/5.png)

Như ví dụ trên tôi giám sát những disk có tên theo định dạng `sda, sdb, ...`

Sau đó click `Add`. Ta sẽ thấy discovery rule đã được tạo

![](/images/img-multi-disk-zabbix/6.png)

Để giám sát một thông số nào đó trên những disk được tìm thấy ta phải tạo các item cho nó. Để tạo item trên `discovery rule` ta click vào `Item prototypes`

![](/images/img-multi-disk-zabbix/7.png)

Click vào `Create item prototype`

![](/images/img-multi-disk-zabbix/8.png)

Thực hiện tạo item như bình thường. Ở những vị trí thay vì phải khai báo tên disk bạn thay vào đó là tham số macros đã khai báo từ trước để nó nhận giá trị mà discovery rule tìm thấy

![](/images/img-multi-disk-zabbix/9.png)

Như trong ví dụ tôi tạo item giám sát tốc độ đọc trên disk.

Sau khi tạo xong bạn sẽ thấy item đã được tạo

![](/images/img-multi-disk-zabbix/10.png)

Bây giờ bạn có thể thấy metric đẩy về. Lúc này trên máy của tôi có 2 disk.

![](/images/img-multi-disk-zabbix/11.png)

**Tài liệu tham khảo**

https://github.com/MinhKMA/mdt-ghichep-zabbix/blob/master/docs/discovery_disk.md

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>