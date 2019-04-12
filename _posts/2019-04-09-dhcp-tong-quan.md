---
date: 2019-04-08
title: Tìm hiểu giao thức DHCP  
categories:
  - CCNA
description: Tìm hiểu tổng quan về giao thức DHCP
author: anhduc
tags: [CCNA,DHCP]
type: Document
---
Tổng quan về DHCP 
## Lời mở đầu 
DHCP là một giao thức khá là một giao thức phổ biến mà chúng ta hay sử dụng đến để truy cập internet. Hôm nay tôi sẽ giúp mọi người hiểu rõ hơn về DHCP và nguyên lý làm việc của nó ở trong mạng internet. 
## Khái niệm 
DHCP (Dynamic Host Configuration Protocol) là giao thức cấu hình host động. Nó cung cấp cho máy tính địa chỉ ip ; subnet mask; default gateway. Và nó thường được cấp phát bởi DHPC server được tích hợp sẵn trên router.

DHCP giao tiếp bằng UDP và sử dụng port 67 và 68. DHCP server sử dụng port 67 để nghe thông tin từ các client và sử dụng port 68 để reply thông tin 

Ưu điểm khi sử dụng DHCP 
- Tập trung quản trị thông tin cấu hình host
- Cấu hình động các máy
- Cấu hình IP cho các máy một cách liền mạch.
- Sự linh hoạt
- Đơn giản hóa vài trò quản trị của việc cauas hình địa chỉ IP của client.
- Sự linh hoạt 

DHCP làm việc theo mô hình client server và thành phần chính của DHCP là: 
- DHCP client: Là thiết bị dùng để kết nối vào mạng 
- DHCP server: Là thiết bị dùng để cấp phát địa chỉ cho client 

![](/images/img-dhcp/screenshot.png)

## Nguyên lý hoạt động của DHCP 
Thành phần chính của DHCP bao gồm 4 bản tin:
- DISCOVERY
- OFFER
- REQUEST
- ACK 

Quá trình cấp phát địa chỉ IP trong giao thức DHCP bao gồm các bước sau 

![](/images/img-dhcp/2019-04-09_10-01.png)

Kịch bản client xin cấp DHCP từ modem

- Bước 1: Khi muốn có địa chỉ IP để truy cập vào internet thì client sẽ tạo ra bản tin DISCOVERY để yêu cầu cấp phát địa chỉ IP 
- Bước 2: Client chưa biết địa chỉ chính xác của Server cấp phát địa chỉ cho mình do đó nó sẽ gửi bản tin này dưới dạng broadcast.
- Bước 3: Server sẽ nhận bản tin DISCOVERY của client. Sau khi biết client muốn được cấp địa chỉ IP nó sẽ kiểm tra xem địa chỉ IP nào phù hợp để cấp cho client sử dụng 
- Bước 4: Server tạo bản tin OFFER. Gói tin này sẽ lưu trữ thông tin về IP và các thông số cấu hình khác mà client yêu cầu để có thể sử dụng để truy cập internet
- Bước 5: Tất cả các server sẽ gửi bản OFFER dưới dạng broadcast
- Bước 6: Client nhận gói OFFER và nó sẽ chọn ra bản OFFER đầu tiên mà nó nhận được. Nếu không nhận được OFFER nào trong một khoảng thời gian nào đó thì nó sẽ gửi lại DISCOVERY một lần nữa
- Bước 7: Client tạo ra gói REQUEST. Và gửi dưới dạng broadcast tới tất cả các server. Server nào được nhận OFFER sẽ mang ý nghĩa là nó đồng ý nhận IP. Server nào không được nhận OFFER thì thông báo là không nhận OFFER đó
- Bước 8: Server nhận bản tin REQEST. Các server không được nhận OFFER sẽ bỏ qua gói tin này. Gói tin nào được nhận OFFER sẽ nhận và xử lý nó. Nó sẽ kiểm tra sem IP này còn sử dụng được hay không. Nếu còn sử dụng được thì nó sẽ ghi lại thông tin và gửi lại gói tin PACK cho client. Còn nếu không thì nó sẽ gửi lại PNAK để quay lại bước 1.

## Hướng dẫn sử dụng tcpdump bắt và phân tích gói tin
`tcpdump` là một câu lệnh dùng để bắt và phân tích gói của một giao thức trong máy tính. Dưới đây là hướng dẫn một số cách dùng của lệnh `tcpdump`. Dưới đây là một số option của tcpdump thường xuyên sử dụng và tôi sẽ liệt kê chúng ở bên dưới 

1. Bắt gói tin từ một giao diện ethernet cụ thể
```
tcpdump -i (tên_ethernet)
```
```
sudo tcpdump -i enp37s0
[sudo] password for anhduc: 
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp37s0, link-type EN10MB (Ethernet), capture size 262144 bytes
```
2. bắt gói tin với số lượng xác định 
```
tcpdump -c (number) -i (ethernet)
```
```
anhduc@anhduc:~$ sudo tcpdump -c 2 -i enp37s0
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp37s0, link-type EN10MB (Ethernet), capture size 262144 bytes
21:20:55.763211 IP a96-16-169-82.deploy.static.akamaitechnologies.com.https > anhduc.53432: Flags [P.], seq 1149191083:1149191114, ack 3662912889, win 294, options [nop,nop,TS val 597743568 ecr 1334154960], length 31
21:20:55.764136 IP anhduc.53432 > a96-16-169-82.deploy.static.akamaitechnologies.com.https: Flags [P.], seq 1:36, ack 31, win 486, options [nop,nop,TS val 1334174961 ecr 597743568], length 35
2 packets captured
6 packets received by filter
0 packets dropped by kernel
```
3. Bắt thông tin ghi vào một file 
```
tcpdump -w (tên_file)
```
4. Bắt gói tin với một giao thức cụ thể 
```
tcpdump -i (tên_ethernet) (tên giao thức)
```
```
anhduc@anhduc:~$ sudo tcpdump -c 2 -i enp37s0 udp
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp37s0, link-type EN10MB (Ethernet), capture size 262144 bytes
09:48:10.595405 IP6 anhduc.49786 > hkg12s01-in-x02.1e100.net.443: UDP, length 23
```
5. Nhận các gói tin trên một cổng cụ thể thông qua tcpdump port
```
tcpdump -i (eth0) port (22)
```
có rất nhiều các option khác nữa bạn có thể thao khảo các option của nó [tại đây](https://securitydaily.net/phan-tich-goi-tin-15-lenh-tcpdump-duoc-su-dung-trong-thuc-te/)

phần trên tôi đã nói về cách để bắt một gói tin để có thể phân tích được gói tin đó thì chúng ta sử dụng phần mềm wireshark để có thể phần tích được nó. Dưới đây tôi sẽ thực hiện một bài lab nhỏ để các bạn có thể hiểu hơn được về cách làm việc của `tcpdump` và phân tích gói tin bằng wireshark

## Kịch bản sử dụng tcpdump 
Tôi sẽ xin ngừng cấp địa chỉ IP tại máy tính của mình sau đó xin cấp lại địa chỉ IP và bắt gói tin trong quá trình máy tính của tôi xin cấp địa chỉ IP để các bạn có thể thấy phương pháp hoạt động mà chúng ta sử dụng. Ta sẽ sử dụng 2 terminal để có thể vừa bắt gói tin bằng `tcpdump` và vừa xin cấp ip bằng `dhclient`
## Thực hiện 
Đầu tiên chúng ta ngừng việc sử dụng ip của máy tính 
```
anhduc@anhduc:~$ sudo dhclient -r 
```
Trước khi xin cấp lại địa chỉ Ip thì ta sử dụng lệnh tcpdump để có thể bắt gói tin với giao thức DHCP. Gói tin của giao thức DHCP sử dụng port của UDP để giao tiếp chính vì thế mà ta phải sử dụng lệnh tcpdump như sau 
```
anhduc@anhduc:~$ sudo tcpdump udp -i enp37s0 -w dhcp.pcap 
```
Trong thời gian lệnh `tcpdump` tạo list danh sách gói tin để  bắt thì ngay sau đó chúng ta xin cấp lại địa chỉ IP mới với lệnh dhclient 
```
anhduc@anhduc:~$ sudo dhclient 
```
Kết qủa của tôi thu được sau khi sử dụng 2 câu lệnh đó là thu được một file có chứa thông tin và cách thức hoạt của giao thức DHCP như sau. 
```
anhduc@anhduc:~$ sudo tcpdump udp -i enp37s0 -w dhcp.pcap 
[sudo] password for anhduc: 
tcpdump: listening on enp37s0, link-type EN10MB (Ethernet), capture size 262144 bytes
^C8 packets captured
8 packets received by filter
0 packets dropped by kernel
```

## Tổng kết
Như chúng ta đã thấy được cách sử dụng của lệnh tcpdump và cách thức hoạt động của giao thức DHCP. Các gói tin trong DHCP ghi những gì và nội dung của nó bao gồi những gì? Nếu bạn muốn biết điều đó thì chúng ta hãy cùng phân tích gói tin đó và cùng tìm hiểu DHCP ở trong KVM ở bài tiếp theo nha. 
Chúc các bạn thanh công!
Thực hiện bởi [cloud365](https://cloud365.vn/)
Written by [Nguyễn Anh ĐỨc](https://nhanhoa.com/)