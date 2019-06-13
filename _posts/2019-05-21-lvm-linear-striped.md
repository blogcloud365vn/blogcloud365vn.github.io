---
date: 2019-04-08
title: Tìm hiểu Logical Volume Manager(LVM) linear và striped 
categories:
  - LINUX TUTORIAL
description: Tìm hiểu cách lưu trữ ổ đĩa linear và striped LVM 
author: anhduc
tags: [LVM,LINUX]
type: Document
---

## Lời mở đầu
Ở bài trước chúng ta đã cùng nhau tìm hiểu về công nghệ LVM là như thế nào. Vậy ở một disk sẽ lưu trữ dữ liệu ra sao và kiểu lưu dữ liệu nào sẽ là tối ưu. Ở bài này tôi sẽ giới thiệu cho bạn biết hai kiểu lưu trữ trong disk là linear và striped 

## Phân biệt hai kiểu lưu trữ 
Mô hình hai kiểu lưu trữ 

![](/images/img_lvm/linear_striperd/27-linear-vs-striped-logical-volume-overview.png)

Khi ta lưu trữ ổ dữ liệu vào ổ đĩa thì ta sẽ có hai kiểu lưu trữ như trên đó là linear và striped. Giả sử ta có các phân vùng từ b tới i như trên thì các kiểu lưu trữ sẽ được lưu trữ như sau 
- Linear : Dữ liệu sẽ được lưu hết phân vùng này rồi bắt đầu chuyển sang phân vùng khác để lưu trữ
- Striped: sẽ chia đều các dữ liệu ra và ghi vào các phân vùng đã có. Và cách chia dữ liệu ra bao nhiêu thì được định sẵn bởi người cài đặt nó

## Ưu điểm và nhược điểm của hai kiểu lưu trữ
**Linear**
- Ưu điểm : Các dữ liệu tập trung vào một phân vùng sẽ dễ dàng quản lý
- Nhược điểm : Khi bị mất dữ liệu sẽ mất hết dữ liệu của một phần đó. Làm việc chậm hơn bởi vì chỉ có một phân vùng mà trong khi các khu vừng khác không hoạt động

**Striped**
- Ưu điểm: Tốc độ sẽ nhanh hơn vì tất cả các phân vùng sẽ cùng làm việc. Tốc độ đọc và ghi cũng nhannh hơn phương pháp Linear
- Nhược điểm: Khi mất dữ liệu ở một phân vùng thì sẽ bị mất và ảnh hưởng rất nhiều dữ liệu bởi vì mỗi dữ liệu đều được lưu ở nhiều phân vùng khi sử dụng phương pháp striped

## Kịch bản và chuẩn bị
Để có thể hiểu rõ hơn về hai cách lưu trữ và kiểm tra kết quả thì tôi sẽ thực hiện một bài lab để giúp các bạn nhìn rõ hơn về nó. Đầu tiên tôi sẽ tạo ra một VM gồm 3 disk. Sau đó tạo ra 2 logical có 2 kiểu lưu trữ là linear và striped rồi sau đó thực hiện quá trình kiểm tra và giám sát sự đọc ghi của các logical. Ta phải chuẩn bị VM như sau
- Bao gồm 3 disk : vda, vdb và vdc 
- Cài các gói sau : wget, bwn-ng 

## Thực hiện
Bước 1 : Đầu tiên ta cài gói wget cho VM bằng lệnh sau 
```
yum install wget
```

![](/images/img_lvm/linear_striperd/screenshot_8.png)

Bước 2 : Sau khi download xong gói wget ta sử dụng wget để cài lệnh giám sát quá trình đọc ghi ổ đĩa bwn-ng 

![](/images/img_lvm/linear_striperd/screenshot.png)

Bước 3 : Các gói cần thiết đã cài sau đó ta đến bước tạo ra các phân vùng để tạo 2 logical có 2 kiểu lưu trữ riêng biệt

![](/images/img_lvm/linear_striperd/screenshot_1.png)

Bước 4: Tiếp theo ta sẽ tạo ra group-volume như bài trước về lvm tổng quan tôi đã hướng dẫn các bạn làm. Sau đó kiểm tra lại bằng lệnh `vgs`

![](/images/img_lvm/linear_striperd/screenshot_2.png)

Bước 5: Ta sẽ tạo ra một logical với kiểu lưu trữ là linear và một logical với kiểu lưu trữ là striped. Ở đây tôi sẽ tạo ra logical có tên là `linear_logical` với group1 theo cú pháp 
```
lvcreate --extents (số %)FREE --name (tên logical)
```
và `striped_logical` với group2 theo cú pháp 
```
lvcreate --extents N%FREE --stripes (số physical) --stripesize (số dung lượng) --name (tên logical) (tên group )
```

![](/images/img_lvm/linear_striperd/screenshot_3.png)

Bước 6: Sau đó ta đi tạo định dạng cho logical để có thể mount lại nó vào thư mục và dùng chúng 

![](/images/img_lvm/linear_striperd/screenshot_4.png)

Bước 7: Ta mount nó lại vào cây thư mục root là có thể sử dụng chúng. Và để kiểm tra lại xem logical đã được mount hay chưa ta sử dụng lệnh `df -h` 

![](/images/img_lvm/linear_striperd/screenshot_5.png)

Bước 8 sau khi đã thực hiện xong việc tạo ra 2 logical thì ra sẽ dùng lệnh dd copy file root vào 2 logical này để xem tốc độ độc ghi của nó và cách lưu trữ dữ liệu xem đúng như lý thuyết hay không.
- Sử dụng với linear ta sử dụng 2 tab terminal với 2 lệnh sau 

![](/images/img_lvm/linear_striperd/screenshot_6.png)

![](/images/img_lvm/linear_striperd/photo_2019-05-20_19-59-59.jpg)

**kết quả ta thấy được rằng chỉ có ổ vdb1 là đang chạy để  lưu trữ khi ta copy phân vùng Ta để ý rằng khi copy vào logical linear sẽ mất 29,8092s ghi ở dòng cuối cùng cuả lệnh dd sau khi hoàn thành**

![](/images/img_lvm/linear_striperd/screenshot_9.png)

![](/images/img_lvm/linear_striperd/screenshot_7.png)

**Còn kết quả ở logical striped ta thấy rằng cả physical vdc2 và vdb2 cùng chạy để  có thể lưu trữ được khi ta copy phân vùng. Và khi ta để ý rằng copy sẽ mất 19,2649s là hoàn thành được ghi ở dòng cuối cùng của lệnh dd sau khi hoàn thành**

## Kết Luận
Ở bài vừa qua tôi đã giới thiệu về hai kiểu logical mà khi chúng ta tạo ra có thể chọn. Mỗi một kiểu sẽ có những ưu nhược điểm khác nhau. Và từ đó tôi đã chứng minh được rằng nó đúng như lý thuyết mà tôi nên ra ở trên. Các bạn hãy chọn kiểu logical nào phù hợp với công việc của mình. 

Chúc các bạn thành công!

Thực hiện bởi [cloud365](https://cloud365.vn/)

Written by [Nguyễn Anh Đức](https://nhanhoa.com/)