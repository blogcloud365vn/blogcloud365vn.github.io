---
title: Cấu hình VLAN, TRUNK - Khái niệm và cấu hình cơ bản
categories:
  - Windows
  - Linux
  - Other
description: Cấu hình VLAN, TRUNK - Khái niệm và cấu hình cơ bản
author: canhdx
tags: [Beginer, Windows, Linux, Other]
type: Document
---

![](https://i.imgur.com/oNi9e15.png) 
<center><h1 style="color:Tomato;"><b>Cấu hình VLAN, TRUNK - Khái niệm và cấu hình cơ bản</b></h1></center>

Read time: 10 phút thôi

Nay được giao cho mấy con Server để cấu hình bao gồm tả phí lù loại để dựng cụm Lab cho anh em thực tập: tay phải 1 em 3750, tay trái 1 em ESXi6.5, lại thêm 1 em Ubuntu, rồi thêm CentOS, rồi lại có thêm Win2k12. Nhiệm vụ là cấu hình VLAN cho mấy em này hòa thuận với nhau dù mỗi em có 3,4 đường kết nối gì đó.

## Thực đơn gọi món nhanh cho bạn

[1. Khái niệm](#khainiem)

[2. Cấu hình VLAN trên SW Cisco](#vlancisco)

[3. Cấu hình trên Windows Server để nhận VLAN](#vlanwin)

[4. Cấu hình trên interface Ubuntu Server để nhận VLAN ](#vlanubuntu)

[5. Cấu hình trên interface CentOS để nhận VLAN](#vlancentos)

[6. Cấu hình trên VMesxi đển nhận VLAN](#vlanvmware)

[7. Nguồn tham khảo](#nguon)

## 1. Khái niệm mà có thể ai cũng biết <a name='khainiem'></a>

- VLAN (Virtual Local Area Networks) được sử dụng để chia 1 mạng vật lý thành nhiều mạng con nhỏ tùy mục đích sử dụng. 
- Tiêu chuẩn mà VLAN sử dụng là IEEE 802.1q
- Mỗi một VLAN được gán một id cụ thể có giá trị từ 1-4094 trong đó id bằng 1 thường được sử dụng để quản lý vì vậy không nên sử dụng id này cho các VLAN mới của bạn .

## 2. Cấu hình VLAN,TRUNK trên SW Cisco <a name='vlancisco'></a>

Dưới đây sẽ hướng dẫn cơ bản cho các bạn cấu hình VLAN, Trunk trên đơn Switch

### 2.1 Cấu hình VLAN 
Truy cập và kiểm tra VLAN hiện có 
```
Switch>enable
Switch#show vlan 
```

Tạo VLAN
```
Switch# configure terminal
Switch(config)# vlan 10 
Switch(config-vlan)# name vlan10
Switch(config)# vlan 20
Switch(config-vlan)# name vlan20
Switch(config-vlan)# exit
```

Xóa VLAN 
```
switch(config)# no vlan 10 
```

Kiểm tra VLAN vừa tạo 
```
Switch# show vlan 
```

Tiến hành xác đinh port mà Server, thiết bị cần access VLAN tiến hành cấu hình VLAN cho port đó 
```
Switch(config)# interface range F0/1-F0/2
Switch(config-if-range)# switchport access vlan 10

Switch(config)# interface F0/3
Switch(config-if)# switchport access vlan 20
```

### 2.2 Thế còn với việc chúng ta có 1 đường kết nối mà muốn sử dụng nhiều VLAN trên này thì sao? Trunking thôi chứ sao nữa

Cấu hình trunking trên SW Cisco

Truy cập 
```
Switch>enable
```

Xác định port muốn cấu hình trunking 
```
Switch# configure terminal
Switch(config)# interface F0/3
```

Thao tác cấu hình như sau 
```
Switch(config-if)# switchport trunk encapsulation dot1q
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit
```

### 2.3 Switch port, VTP, InterVLAN Routing

Ở phần 2.1 và 2.2 chúng ta đã cấu hình được cơ bản về VLANs và Trunk trên đơn Switch. Thực ra đối với môi trường thực tế thì việc cấu hình còn phức tạp hơn vì nó kết nối đến nhiều Sw, router khác nhau nữa. 

Việc đồng bộ dữ liệu VLANs được cấu hình giữa các thiết bị là cần thiết nhưng nó không nằm trong phạm vi của bài viết các bạn có thể tham khảo cụ thể tại bài viết sau 
[Cấu hình Switch port, VLANs, Trunk, VTP, InterVLAN Routing]()

## 3. Cấu hình trên Windows Server để nhận VLAN <a name='vlanwin'></a>

Đối với interface kết nối đến port trên Switch là `access vlan X` thì đơn giản chỉ cần đặt IP cho Server thôi, không phải thao tác gì khác. 

Đối với interface kết nối đến port trên Switch là `mode trunk` thì chúng ta phải thực hiện cấu hình như sau

Mở `Run` gõ `devmgmt.msc` để mở Device Manager
![](images/image-vlan-trunk/blog-vlan-06.png)

Trong `Device Manager` click chuột phải vào NIC kết nối đến Switch chọn `Properties`
![](images/image-vlan-trunk/blog-vlan-07.png)

Click vào `Advanced` tab kéo xuống chọn `VLAN ID` set giá trị ID VLAN 
![](images/image-vlan-trunk/blog-vlan-08.png)

Hoàn tất cấu hình


## 4. Cấu hình trên interface Ubuntu Server để nhận VLAN <a name='vlanubuntu'></a>

Vẫn như phía trên interface kết nối đến port trên Switch là `access vlan X` thì đơn giản chỉ cần đặt IP cho Server thôi, không phải thao tác gì khác. 

Interface kết nối đến port trên Switch là `mode trunk` thì chúng ta phải thực hiện cấu hình như sau

Cài đặt thêm gói cho Server 
```
sudo apt-get install vlan
```

Cấu hình enable mode 8021q hỗ trợ VLAN 
```
sudo su -c 'echo "8021q" >> /etc/modules'
```

Create 1 virtual interface `eth1.10` access vlan10 trên interface vật lý `eth1`
```
auto eth1
iface eth1 inet manual

auto eth1.10
iface eth1.10 inet static
    address 10.0.0.1
    netmask 255.255.255.0
    vlan-raw-device eth1
```

Đừng quên restart network của bạn
```
systemctl restart networking
```

Kiểm tra Interface cho VLAN mới được tạo sẽ có dạng như sau 
```
eth0.10@eth0: 
```

## 5. Cấu hình trên interface CentOS để nhận VLAN <a name='vlancentos'></a>

Lại vẫn giống như phía trên :D interface kết nối đến port trên Switch là `access vlan X` thì đơn giản chỉ cần đặt IP cho Server thôi, không phải thao tác gì khác. 

Interface kết nối đến port trên Switch là `mode trunk` thì chúng ta phải thực hiện cấu hình như sau

Cấu hình interface vật lý `/etc/sysconfig/network-scripts/ifcfg-ethX`
```
DEVICE=eth1
TYPE=Ethernet
BOOTPROTO=none
ONBOOT=yes
```

Để cấu hình interface cho VLAN chúng ta tạo thêm virtual interface `/etc/sysconfig/network-scripts/ifcfg-ethX.<vlan-id>`
```
DEVICE=eth1.192
BOOTPROTO=none
ONBOOT=yes
IPADDR=192.168.1.1
PREFIX=24
NETWORK=192.168.1.0
VLAN=yes
```
> Lưu ý virtual interface khi chúng ta cấu hình VLAN trên CentOS bắt buộc phải là dấu "." ethX.\<vlan-id>

Và cũng đừng quên bước quan trọng 
```
systemctl restart network
```

Kiểm tra Interface cho VLAN mới được tạo sẽ có dạng như sau 
```
eth0.10@eth0: 
```

## 6. Cấu hình trên VMesxi đển nhận VLAN <a name='vlanvmware'></a>

Nói chung là các port kết nối đến port trên Switch là `access vlan X` chỉ cần set ip cho interface, còn port đó là `mode trunk` thì mới phải thao tác cấu hình

Truy cập tab `Configuration` --> `Networking` --> `Add Networking`
![](images/image-vlan-trunk/blog-vlan-01.png)

Chọn `Virtual Machine` để add thêm 1 label network cho VLAN mới 
![](images/image-vlan-trunk/blog-vlan-02.png)

Chọn Interface mà chúng ta gắn vào Switch VLAN 
![](images/image-vlan-trunk/blog-vlan-03.png)

Khai báo VLAN_name và VLAN_id 
![](images/image-vlan-trunk/blog-vlan-04.png)

Hoàn tất create 1 virtual interface kết nối vào VLAN
![](images/image-vlan-trunk/blog-vlan-05.png)


## 7. Nguồn tham khảo <a name='nguon'></a>

Cấu hình VLAN cho Ubuntu: 
https://wiki.ubuntu.com/vlan

## 8. Chốt hạ cho bài viết  <a name='sum'></a>

Bài viết không mang quá nhiều chất xám và không nằm ngoài mục đích note lại tài liệu cho các đồng môn trong môn phái. Tiếp đó là .... dành cho chính bạn khi bạn cần. 