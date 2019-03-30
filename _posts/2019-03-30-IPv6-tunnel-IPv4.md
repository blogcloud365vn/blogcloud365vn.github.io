---
date: 2019-03-30
title: Sử dụng IPv6 trên hạ tầng IPv4
categories:
  - IPv6
description: Sử dụng IPv6 trên hạ tầng IPv4
author: minhnv
tags: [IPv6, GNS3, CCNA]
type: Document
---


# Sử dụng IPv6 trên hạ tầng IPv4 có sẵn.

## Kịch bản

You are a kumanthong in network designing. Công ty có kế hoạch nâng cấp hạ tầng mạng của họ và để đảm bảo sẵn sàng cho họ triển khai IPv6 trong tương lai. Tuy nhiên, xương sống của hạ tầng mạng vẫn dựa trên IPv4 và bạn không được phép thực hiện bất kỳ thay đổi nào...

Chúng ta hãy xem viêc tạo tunneling để giải quyết bài toán trên

## Mô hình 

<img src='https://i.imgur.com/YFoFF5L.png'>

Môi trường: 

- Thực hiện bài lab trên GNS3
- IOS router là: `c3640-jk9s-mz.124-16.bin`. Bạn có thể tải xuống ở < href="https://doc.lagout.org/network/Cisco/IOS%20Collection/c3640/c3640-jk9s-mz.124-16.bin">đây>.

## Mục tiêu bài LAB 

- Cấu hình IPv4 và IPv6 addresses
- Chúng ta không thay đổi bất cứ gì trên router `ISP`
- Định tuyến OSPF trong miền IPv4
- Cấu hình `IPv6 over IPv4 tunnel` giữa router `HP69_HN` và `HP69_HCM` thông qua `3000::/64 prefix`
- Định tuyến RIPNG trên các routers `HN`, `HCM`, `HP69_HN`, `HP69_HCM`
- Đảm bảo bạn có kết nối giữa mạng `2000::/64` và `4000::/64`.

### Here we go!

## Bước 1: Chuẩn bị môi trường

Ở đây tôi thực hiện giả lập mô hình mạng IPv6 của tôi gồm có 2 site : HN và HCM được chạy trên hạ tầng IPv4 như hình trên. 

Phần chuẩn bị này tôi chia là hai phần:

    + Phần thứ nhất: Thiết lập IPv4 cho domain sau đó sử dụng định tuyến OSPF giữa các routers trong domain 
    + Phần thứ hai: Thiết lập IPv6 cho mô hình mạng 

## Bước 1.1: Thiết lập domain IPv4 

##### Cấu hình IPv4

- Trên router `HP69_HN`

```
HP69_HN#enable 
HP69_HN#configure terminal 
HP69_HN(config)#interface ethernet 1/0
HP69_HN(config-if)#ip address 192.168.23.1 255.255.255.0
HP69_HN(config-if)#no shutdown 
```

- Trên router `IPS`

```
ISP#enable
ISP#configure terminal 
ISP(config)#interface ethernet 0/0
ISP(config-if)#ip address 192.168.23.2 255.255.255.0
ISP(config-if)#no shutdown
ISP(config-if)#exit
ISP(config)#interface ethernet 1/0
ISP(config-if)#ip address 192.168.34.3 255.255.255.0
ISP(config-if)#no shutdown 
```

- Trên router `HP69_HCM`

```
HP69_HCM#enable 
HP69_HCM#configure terminal 
HP69_HCM(config)#interface ethernet 0/0
HP69_HCM(config-if)#ip address 192.168.34.4 255.255.255.0
HP69_HCM(config-if)#no shutdown 
```

##### Định tuyến OSPF

- Trên router `HP69_HN`

```
HP69_HN#configure terminal 
HP69_HN(config)#router ospf 1
HP69_HN(config-router)#network 192.168.23.0 0.0.0.255 area 0
```

- Trên router `IPS`

```
ISP#configure terminal 
ISP(config)#router ospf 1
ISP(config-router)#network 192.168.23.0 0.0.0.255 area 0
ISP(config-router)#network 192.168.34.0 0.0.0.255 area 0
```

- Trên router `HP69_HCM`

```
HP69_HCM#configure terminal 
HP69_HCM(config)#router ospf 1
HP69_HCM(config-router)#network 192.168.34.0 0.0.0.255 area 0
```

##### Kiểm tra cấu hình

```
HP69_HCM#ping 192.168.23.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.23.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/35/44 ms
HP69_HCM#
```

## Bước 1.2: Thiết lập IPv6

Trong phần này mình thiết lập địa chỉ IPv6 cho các routers và sử dụng định tuyến RIP-NG 

- Trên router `HN`

```
HN#enable
HN#configure terminal 
HN(config)#ipv6 unicast-routing 
HN(config)#ipv6 router rip LAB
HN(config-rtr)#interface ethernet 0/0
HN(config-if)#ipv6 address 2000::2/64
HN(config-if)#ipv6 rip LAB enable
HN(config-if)#no shutdown
```

- Trên router `HCM`

```
HCM#enable
HCM#configure terminal 
HCM(config)#ipv6 unicast-routing 
HCM(config)#ipv6 router rip LAB
HCM(config-rtr)#exit 
HCM(config)#interface ethernet 0/0
HCM(config-if)#ipv6 address 4000::2/64
HCM(config-if)#ipv6 rip LAB enable 
HCM(config-if)#no shutdown
```

- Trên router `HP69_HN`

```
HP69_HN#enable 
HP69_HN#configure terminal 
HP69_HN(config)#ipv6 unicast-routing 
HP69_HN(config-rtr)#ipv6 router rip LAB
HP69_HN(config-rtr)#exit 
HP69_HN(config)#interface ethernet 0/0
HP69_HN(config-if)#ipv6 address 2000::1/64
HP69_HN(config-if)#ipv6 rip LAB enable 
HP69_HN(config-if)#no shutdown
```


- Trên router `HP69_HCM`

```
HP69_HCM#enable 
HP69_HCM#configure terminal 
HP69_HCM(config)#ipv6 unicast-routing 
HP69_HCM(config)#ipv6 router rip LAB
HP69_HCM(config-rtr)#exit 
HP69_HCM(config)#interface ethernet 1/0
HP69_HCM(config-if)#ipv6 address 4000::1/64
HP69_HCM(config-if)#ipv6 rip LAB enable 
HP69_HCM(config-if)#no shutdown
```

## Bước 2: Thiết lập tunnel 

Cấu hình tunnel IPv6 giữa `HP69_HN` và `HP69_HCM` sử dụng dải địa IPv6 là 3000::/64 prefix

- Trên router `HP69_HN`

```
HP69_HN#configure terminal 
HP69_HN(config)#interface tunnel 0
HP69_HN(config-if)#tunnel source ethernet 1/0
HP69_HN(config-if)#tunnel destination 192.168.34.4
HP69_HN(config-if)#tunnel mode ipv6ip 
HP69_HN(config-if)#ipv6 address 3000::1/64
HP69_HCM(config-if)#ipv6 rip LAB enable 
```

- Trên router `HP69_HCM`

```
HP69_HCM#configure terminal 
HP69_HCM(config)#interface tunnel 0
HP69_HCM(config-if)#tunnel source ethernet 0/0
HP69_HCM(config-if)#tunnel destination 192.168.23.1
HP69_HCM(config-if)#tunnel mode ipv6ip 
HP69_HCM(config-if)#ipv6 address 3000::2/64
HP69_HCM(config-if)#ipv6 rip LAB enable 
```

## Bước 3: Kiểm tra lại kết nối site HN và site HCM

Hey, bây giờ hãy cùng nhâm nhi một tách trà và kiểm tra lại kết nối giữa 2 site IPv6

- Trên router `HN`

```
HN#ping 4000::2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 4000::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 40/48/56 ms
HN#
```

- Trên router `HCM`


```
HCM#ping 2000::2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2000::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 40/47/56 ms
HCM#
```

Chúc các bạn có một buổi tối tuyệt vời bên hệ thống LAB. 