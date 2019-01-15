---
date: 2019-01-15
title: Hướng dẫn cấu hình Nginx làm load balancing cho Apache trên CentOS 7
categories:
  - Linux
description: Tài liệu hướng dẫn cấu hình Nginx làm load balancing cho Apache trên CentOS 7
author: thanhnb
tags: [Nginx, Linux, CentOS7]
type: Document
---

Tài liệu hướng dẫn hướng dẫn cấu hình Nginx làm load balancing cho Apache<br>
## 1. Chuẩn bị
- Đăng ký trên 'portal365.vn' 3 Máy ảo CentOS 7 với cấu hình 

### Mô hình


### Phần hoạch
| Hostname | Hardware                      | Interface                                               |
|----------|-------------------------------|---------------------------------------------------------|
| node1    | 2 Cpu - 2gb Ram - 25 gb Disk | ens160: 10.10.11.20 (MNGT) - ens192: 10.10.11.86 (REPL) |
| node2    | 2 Cpu - 2gb Ram - 25 gb Disk | ens160: 10.10.11.24 (MNGT) - ens192: 10.10.11.87 (REPL) |
| node3    | 2 Cpu - 2gb Ram - 25 gb Disk | ens160: 10.10.11.27 (MNGT) - ens192: 10.10.11.88 (REPL) |


Thực hiện bởi [cloud365.vn](https://cloud365.vn/)
