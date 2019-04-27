---
date: 2019-03-21
title: Hướng dẫn sử dụng câu lệnh stress trên linux
categories:
  - Linux
description: Tài liệu hướng dẫn sử dụng câu lệnh stress trên linux
author: niemdt
tags: [Linux]
type: Document
---

## Tổng quan

Stress là một câu lệnh được sử dụng để áp một mức tải cho hệ thống để kiểm tra khả năng chịu tải của nó trong thực tế. Nó sẽ tạo ra các process sử dụng rất nhiều tài nguyên của hệ thống như CPU, RAM hay disk để ta có thể kiểm tra được khả năng và ngưỡng mà hệ thống của ta hoạt động tốt nhất. Nó cũng rất có ích để tái hiện lại trạng thái tải cao gây ra lỗi hệ thống để ta có thể tìm cách xử lý.

## Cài đặt

Để sử dụng được công cụ stress bạn cần cài đặt các gói stress 

**Trên CentOS**

```
yum install stress
```

**Trên Ubuntu**

```
apt-get install stress
```

## Sử dụng 

Bạn có thể có thể stress hệ thống của bạn với các thông số CPU, memory, io, hdd hoặc stress cùng lúc nhiều thông số. Dưới đây tôi sẽ ví dụ một số trường hợp cụ thể để bạn có thể biết cách sử dụng nó.

**Test CPU**

Để test CPU ta dùng option `--cpu` hoặc `-c`

Câu lệnh 

```
stress -c 1
```

Bạn có thể thấy CPU trước khi sử dụng câu lệnh `stress`

![](/images/img-command-stress/1.png)

Tôi tiến hành chạy câu lênh stress

![](/images/img-command-stress/2.png)

Và đây là CPU

![](/images/img-command-stress/3.png)

Với câu lệnh này thì 1 tiến trình được tạo ra để tính toán hàm `sqrt()` của một số bất kỳ. Như vậy thì CPU sẽ tính toán liên tục cho đến khi bạn nhấn `Ctrl C` hoặc bạn có thể đặt thời gian cho nó bằng cách thêm vào option `--timeout` hoặc `-t`. Ví dụ 

```
stress --cpu 1 --timeout 30
``` 

Thì câu lệnh này sẽ tự động dừng sau 30s

Vì mỗi hàm `sqrt()` được đẩy vào sẽ là một tiến trình và nó sẽ chiếm trọn CPU. Như vậy điều gì sẽ xảy ra khi số tiến trình nhiều hơn số CPU mà ta đang có. Ở đây tôi đẩy vào 2 tiến trình lớn gấp đôi số CPU trong máy của tôi

```
stress -c 2
```

Và đây là kết quả

![](/images/img-command-stress/4.png)

Lúc này mỗi tiến trình sẽ chỉ sử dụng 50% CPU đồng nghĩa với việc tốc độ xử lý sẽ bị giảm đi một nửa

**Test memory**

Để đẩy tải vào RAM để test ta dùng option `-m` hoặc `--vm`

Câu lệnh 

```
stress -m 1
```

Câu lệnh này sẽ sử dụng hàm `malloc()` để cấp phát vùng nhớ cho 1 process ở đây sẽ cấp là 256M. Hoặc ta có thể chỉ ra dung lượng cấp phát cho một process bằng option `--vm-bytes`. Ví dụ cấp cho mỗi process là `500M`

```
stress -m --vm-bytes 500M
```

Bạn có thể thấy mức độ sử dụng RAM trước khi sử dụng câu lệnh stress

![](/images/img-command-stress/5.png)

Ta thấy RAM sử dụng ít và bộ nhớ swap chưa được sử dụng. Bây giờ ta chạy câu lệnh stress

```
[root@niemdt1 ~]# stress -m 1 --vm-bytes 600M
stress: info: [10768] dispatching hogs: 0 cpu, 0 io, 1 vm, 0 hdd
```

Và ta quan sát thấy RAM lúc này

![](/images/img-command-stress/6.png)

Ta thấy giá trị RAM thay đổi liên tục là bởi vì câu lệnh này sử dụng liên tục hai hàm `malloc` để cấp phát vùng nhớ sau đó dùng hàm `free` để giải phóng nó.

**Test disk**

Bạn cũng có thể xem hệ thống của bạn hoạt động như thế nào khi bị ràng buộc về I/O. Bạn sử dụng option `-i`

Câu lệnh

```
stress -i 1
```

Câu lệnh này sẽ gọi đến hàm `sync()`

Ngoài ta bạn có thể test khả năng ghi dữ liệu của disk bằng cách dùng option `-d`. 

```
stress -d 1 --timeout 60s
```

Câu lệnh tiến hành ghi một file có dung lượng 1G vào disk. Bạn có thể chỉ ra dung lượng của nó bằng cách thêm option `--hdd-bytes`. Ví dụ muốn ghi 1 file 2G

```
stress -d 1 2G --timeout 1m
```

Bạn không phải lo dọn dẹp các file này sau khi chạy câu lệnh vì nó sẽ tự dọn dẹp các file này cho bạn khi kết thúc câu lệnh.

## Tổng kết

Trên đây là một vài ví dụ cụ thể về câu lệnh stress. Câu lệnh này có thể giúp ích cho bạn khá nhiều. Trong bài viết chắc chắn còn nhiều thiếu sót rất mong được sự góp ý của các bạn.

Chúc bạn thành công!

**Tài liệu tham khảo**

http://ktaraghi.blogspot.com/2014/10/stress-command.html

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>