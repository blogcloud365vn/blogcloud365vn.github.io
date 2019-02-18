---
title: Hướng dẫn cấu hình linux bridge trên KVM
categories:
  - Linux
description: Tài liệu hướng dẫn cấu hình linux bridge trên KVM
author: niemdt
tags: [KVM, Linux, Centos]
type: Document
---

## Tổng quan

KVM được biết đến là một cơ sở hạn tầng ảo hóa cho nhân Linux. KVM cũng cung cấp các mô hình mạng trong việc ảo hóa network. Các mô hình bao gồm:

 * NAT
 * Host-only
 * Linux-bridge

Trong đó `Linux-bridge` là một mô hình ảo hóa mạng được hỗ trợ bởi KVM. `Linux bridge` là một công nghệ cung cấp switch ảo để giải quyết vấn đề ảo hóa Network bên trong các máy vật lý.

![](/images/img-linux-bridge/1.png)

Chúng ta có thể thấy rằng có một con switch được tạo ra nằm bên trong của máy vật lý. Các VM kết nối đến đây để có thể liên lạc được với nhau. Nếu muốn liên lạc ra bên ngoài ta có thể kết nối con switch này với card mạng trên máy vật lý của ta (giống như ta dùng dây kết nối switch với router). Ta có thể kết nối switch với 1 hoặc nhiều port.

*Chú ý* ta không thể kết nối switch ảo với card `wireless` do HĐH không hỗ trợ.

## Chuẩn bị

Một máy chạy hệ điều hành Centos7 hoặc Ubuntu đã cài KVM bên trong đó đã có một VM. 

*Chú ý* máy vật lý ở đây là máy cài KVM. Máy này có thể là một máy ảo nhưng ở đây ta coi nó như một server vật lý.

## Cấu trúc linux bridge

![](/images/img-linux-bridge/2.png)

Trong đó:
* **Bridge** ở đây là switch ảo 
* **Tap** hay tap interface là giao diện mạng để các VM kết nối với switch do Linux bridge tạo ra(nó hoạt động ở lớp 2 của mô hình OSI)
* **fd**: Forward data có nhiệm vụ chuyển dữ kiệu từ VM tới switch.

Switch ảo do Linux bridge tạo ra có chức năng tương tự với 1 con switch vật lý.

![](/images/img-linux-bridge/3.png)

Ta có thể thấy rõ hơn cách kết nối của VM ra ngoài internet. Khi máy vật lý của ta có card mạng kết nối với internet(không phải card wireless). Trên switch ảo của ta sẽ phải có đường để kết nối ra ngoài internet(cụ thể là kết nối với card mạng của máy vật lý). Ta có thể hình dung card mạng trên máy vật lý sẽ được gắn trực tiếp vào switch ảo nên ta có thể thấy sau khi add switch ảo và card vật lý có cùng địa chỉ MAC. Và trên card vật lý sẽ không còn địa chỉ IP mà nó được gắn cho switch ảo.

![](/images/img-linux-bridge/4.png)

![](/images/img-linux-bridge/5.png)

Và bây giờ trên các VM muốn giao tiếp với nhau hoặc ra ngoài internet ta chỉ cần kết nối VM đó với switch ảo. Lúc này card mạng trên VM sẽ được gắn với 1 cổng của switch ảo thông qua  tap interface và cổng này có tên là `vnet`. Ở đây có tên là `vnet0`

Khi ta kết nối vào switch ảo các VM sẽ nhận địa chỉ IP cùng với dải địa chỉ IP của card mà ta add và switch và các địa chỉ IP này sẽ được cấp bởi dịch vụ DHCP trên router.

![](/images/img-linux-bridge/6.png)

Với mô hình này gói tin bên trong VM đi ra ngoài mạng sẽ đi từ VM đến thẳng card vật lý gắn với switch ảo và đi ra ngoài mạng.

## Tạo và quản lý linux bridge

Để tạo một linux bridge(switch ảo) ta dùng lệnh 
`brctl addbr tên_switch`

![](/images/img-linux-bridge/8.png)

Tiếp theo là bước add card mạng cho switch dùng lệnh 
`brctl addif tên_switch tên_card`
 * Tên swich ở đây là switch ta vừa mới tạo ở câu lệnh bên trên
 * Tên card ở đây là card mạng trên máy vật lý của ta (trừ card wireless).

![](/images/img-linux-bridge/9.png)

Để kiểm tra những swtich ảo trên máy và những card đã được add vào switch ảo đó ta dùng lệnh 

`brctl show`

![](/images/img-linux-bridge/10.png)

Tiếp sau đó ta tiến hành xin cấp IP cho NIC. Ta dùng các câu lệnh sau để xóa IP của card ens9

`ifconfig tên_card 0` 

![](/images/img-linux-bridge/11.png)

Sau đó xin cấp IP cho bridge bằng lệnh `dhclient tên_bridge`

![](/images/img-linux-bridge/12.png)

*Lưu ý* hai câu lệnh trên chỉ có hiệu lực đến khi ta reboot lại máy vật lý. Để VM có thể nhận được địa chỉ IP ngay cả khi server vật lý bị reboot ta thực hiện ghi các dòng như sau vào file `/ect/network/interfaces`
```
auto testbr
iface testbr inet dhcp
bridge_ports ens9
bridge_stp off # kich hoat che do STP trong bridge
bridge_fd 0 
bridge_maxwait 0
```
Sau đó tiến hành chạy lênh `ifdown -a && ifup -a` để khởi động lại internet.

Và bây giờ ta có thể thấy

Khi chưa dùng 2 câu lệnh trên

![](/images/img-linux-bridge/13.png)

Sau khi dùng 2 lệnh trên

![](/images/img-linux-bridge/14.png)

Bây giờ trên VM có thể kết nối với switch ảo đó.

![](/images/img-linux-bridge/15.png)

Ta có thể thấy VM đã nhận IP

![](/images/img-linux-bridge/16.png)

Mô hình vừa tạo như sau

![](/images/img-linux-bridge/17.jpeg)

## Tổng kết

Đến đây ta đã tạo được mô hình mạng linux bridge và đã đã kết nối VM vào với mô hình mạng đó. Trong bài viết chắc chắn còn nhiều thiếu sót rất mong được sự góp ý của các bạn.

Chúc bạn thành công!

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>