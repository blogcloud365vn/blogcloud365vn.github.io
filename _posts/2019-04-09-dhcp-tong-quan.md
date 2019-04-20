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

Để phân tích được gói tin của giao thức DHCP thì ta phải sử dụng một lệnh được gọi là [tcpdump](https://github.com/duckmak14/thuctapsinh/blob/master/Anhduc/liunux/lab/tcpdump.md)
## Phân tích gói 4 bản tin
![](/images/img-dhcp/screenshot_16.png)

1. DHCP DISCOVERY

![](/images/img-dhcp/screenshot_17.png)

Trong đó 
- 1: là địa chỉ đầu và địa chỉ cuối của gói tin ghi bằng MAC
- 2: là địa chỉ đầu và cuối nhưng được ghi bằng IPv4
- 3: là port mà gói tin đó sử dụng
- 4: địa chỉ IP của client
- 5: MAC của client
- 6: IP client yêu cầu được cấp phát

2.  DHCP OFFER

![](/images/img-dhcp/screenshot_18.png)

Trong đó 
- Option 54: chỉ đính danh DHCP server
- Option 51: thời gian cho thuê địa chỉ IP
- Option 1: địa chỉ subnet Mask
- Option 28 : địa chỉ broadcast
- Option 3 : địa chỉ default gateway
- Option 6 : địa chỉ DNS

3. DHCP Request

![](/images/img-dhcp/screenshot_19.png)

- Option 53: Kiểu tin nhắn
- Option 55: Danh sách tham số yêu cầu
- Option 50: Địa chỉ IP yêu cầu

4.  DHCP ACK

![](/images/img-dhcp/screenshot_20.png)

- Nhiệm vụ của gói tin này là để xác nhận lại thông tin đã cấp cho client
## Tổng kết
Như chúng ta đã thấy được cách sử dụng của lệnh tcpdump và cách thức hoạt động của giao thức DHCP. Nó đã sử dụng 4 gói tin để có thể cấp phát địa chỉ IP cho client vậy thì trong KVM DHCP sẽ hoạt động ra sao chúng ta sẽ cùng nhau phân tích việc đó ở bài DHCP trong KVM.

Chúc các bạn thành công!

Thực hiện bởi [cloud365](https://cloud365.vn/)

Written by [Nguyễn Anh ĐỨc](https://nhanhoa.com/)