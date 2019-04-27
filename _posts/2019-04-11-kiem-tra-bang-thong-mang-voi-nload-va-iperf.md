---
date: 2019-04-11
title: "Kiểm tra băng thông và tốc độ network trên Linux với Iperf và Nload"
categories:
  - Linux
description: Kiểm tra băng thông và tốc độ network trên Linux với Iperf và Nload
author: ManhDV
tags: [Linux, CentOS7]
type: Document
---

## 1. Giới thiệu về công cụ Iperf và Nload

 - **Iperf** là một công cụ miễn phí, dùng để đo lường lượng dữ liệu mạng (throughput) tối đa mà một server có thể xử lý. Công cụ này rất hữu ích để truy tìm ra các vấn đề đối với hệ thống mạng. Iperf còn có thể kiểm tra băng thông tối đa mà đường truyền mạng có thể đáp ứng. 
 
 - **Nload** là một công cụ dòng lệnh để theo dõi lưu lượng mạng và việc sử dụng băng thông theo thời gian thực. Nload giúp giám sát lưu lượng đến (Incoming) và đi (Outgoing) bằng biểu đồ và cung cấp các thông tin như tổng lượng dữ liệu được truyền và mức sử dụng mạng tối thiểu và tối đa.
 
## 2. Sử dụng Iperf và Nload để kiểm tra băng thông mạng
 
Tại bài Lab này chúng ta sẽ sử dụng công cụ Iperf và Nload để thực hiện kiểm tra băng thông tối đa của mạng. Bài test này thường áp dụng để kiểm tra tốc độ tối đa của đường truyền mạng và card mạng. 

# Mô hình 
![1](https://i.imgur.com/SZETxcQ.png) 

---
### Trên Server

IP : 192.168.20.159

OS : Centos 7

Port 10GB : p1p2

- Cài đặt phần mềm kiểm tra : 
```sh
yum install epel-release -y
yum install iperf nload -y
```

- Bật mode iperf server : 
```sh
iperf -s
```

-Tắt firewalld trên Client
```sh
systemctl stop firewalld
```

### Trên Client 

IP : 192.168.20.159

OS : Centos 7

Port 10GB : p1p2

- Cài đặt phần mềm kiểm tra : 
```sh
yum install epel-release -y
yum install iperf nload -y
```

-Tắt firewalld trên Server
```sh
systemctl stop firewalld
```

### Thực hiện kiểm tra
- Trên Client, thực hiện đẩy gói TCP tới server, kiểm tra traffic network trên card 10G của Client. 
- Thực hiện đẩy iperf TCP
```sh
iperf -c 192.168.20.159 -i1 -t 100 -m
```
Giải thích tham số câu lệnh : 
 - **-c** : địa chỉ host của iperf server (192.168.20.159)
 - **-i** : khoảng thời gian giữa 2 lần report kết quả theo giây (1s)
 - **-t** : thời gian thực hiện đẩy traffic theo giây (100s)
 - **-m** : in ra MTU header

![2](https://i.imgur.com/yqbbdsr.png) 

Bandwidth được đẩy lên ~9,3 Gbits/sec nghĩa là card 10GB đã chuẩn.

- Kiểm tra trên network traffic của cả client và server. Sử dụng câu lệnh để nload để xem traffic network trên port p1p2. Để chỉ rõ port p1p2, sử dụng option **-d** như bên dưới : 
```sh
nload -d p1p2
```

- Trên client, interface sẽ có Outgoing traffic ~9 Gbits/sec. 

![3](https://i.imgur.com/pVcEgEl.png) 

- Trên server, interface sẽ có Incoming traffic

![4](https://i.imgur.com/pyoZNeg.png) 

Kết quả như trên nghĩa là 2 card 10G trên Client và Server đã OK.