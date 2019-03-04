---
date: 2019-03-03
title: "Hướng dẫn kiểm tra dung lượng Ram trên hệ điều hành Linux"
categories:
  - Linux
description: Hướng dẫn kiểm tra dung lượng Ram trên hệ điều hành Linux
author: thanhnb
tags: [Linux, CentOS7]
type: Document
---

## Tổng quan

Trong bài viết mình sẽ hướng dẫn các bạn một số cách kiếm tra dung lương bộ nhớ (RAM) sử dụng trong hệ điều hành Linux (Ở đây mình sử dụng OS CentOS 7).

Linux hỗ trợ rất nhiều command khác nhau để kiếm tra dung lượng ram thực tế cũng như dung lượng ram đã sử dụng. Ví dụ như câu lệnh `free` hiển thị tổng dung lượng bộ nhớ vật lý (Physical memory) và dung lượng bộ nhớ chuyển đổi (Swap memory) đã sử dụng. Hoặc câu lệnh `top` để xem dung lượng ram đã sử dụng thời gian thực (realtime)

## Chuẩn bị

Mình sẽ truy cập `https://cloud365.vn/`, đăng ký `gói Cloud VPS B` với cấu hình 2 core, 2 GB ram, 25 GB ở đĩa.

![](/images/image-kiem-tra-dung-luong-ram/cloud365.png)

## Kiểm tra dung lượng bộ nhớ
### Kiểm tra dung lượng bộ nhớ đơn giản
Cách đơn giản nhất để kiếm tra dung lượng bộ nhớ của Cloud VPS là các bạn sử dụng câu lệnh `free`

ở đây mình sẽ thực hiện câu lệnh `free -h`
```
free -h
```

Kết quả

![](/images/image-kiem-tra-dung-luong-ram/pic1.png)

Kết quả cho thấy, Cloud VPS của mình có thông số bộ nhớ ram như sau:
- (1) Tổng dung lượng bộ nhớ bằng 1,8 GB (`total`)
- (2) Đã sử dụng 80 MB (`used`)
- (3) Dung lượng rảnh rỗi bằng 1,5 GB (`free`)
- (4) Dung lượng bộ nhớ sử dụng cho việc lưu đệm bằng 228 MB (`buff/cache`)

Lưu ý:
- Số ram trống tuy chỉ bằng 1.5 GB (`free`) nhưng thực tế bạn có thể sử dụng nhiều hơn thế. Tổng số bộ nhớ có thể sử dụng sẽ bằng `free` + `buff/cache`. Vậy ở đây, số ram thực sự có thể sử dụng được sẽ bằng 1.728 GB.
- Lượng bộ nhớ ram được `buff/cache` thường được sử dụng để cải thiện hiệu năng đọc ghi ổ đĩa. Vì bộ ram sẽ có tốc độ truy vấn, đọc ghi cao hơn rất nhiều so với tốc độ truy vấn, đọc ghi ổ đĩa nên hệ điều hành sử dụng lượng ram còn trống để cài thiện hiệu năng.

Vậy mình đã hướng dẫn các kiểm tra thông số bộ nhớ đơn giản. Tiếp theo mình sẽ hướng dẫn phương pháp nâng cao.

### Kiểm tra dung lượng bộ nhớ nâng cao

Để kiếm tra các thông số chi tiết về bộ nhớ ram, chúng ta sẽ kiểm tra file `/proc/meminfo`. Bản thân câu lệnh `free` cũng sử dụng file `meminfo` để kiếm tra dung lượng bộ nhớ ram của Cloud VPS.

Thống số trong file `/proc/meminfo` khá nhiều nên mình sẽ chỉ tập trung vào các tham số quan trọng về bộ nhớ.

Thực hiện câu lệnh
```
egrep --color 'Mem|Cache' /proc/meminfo
```

![](/images/image-kiem-tra-dung-luong-ram/pic2.png)

Mình sẽ giải thích kết quả ở bên dưới

![](/images/image-kiem-tra-dung-luong-ram/pic3.png)
- (1) `MemTotal`: Đây là tổng số bộ nhớ Ram hiện có (Đơn vị kilobyte). Ở đây giá trị bằng `1882868 kB` tức bằng 1,8 GB đúng như kết quả của câu lệnh `free`

![](/images/image-kiem-tra-dung-luong-ram/pic4.png)
- (2) `MemFree`: Đây là số bộ nhớ ram trống, giá trị bằng `1581892 kB` tức bằng 1,5 GB.


![](/images/image-kiem-tra-dung-luong-ram/pic5.png)
- (3) `MemAvailable`: Đây tổng số Ram có thể sử dụng, giá trị bằng `1616352 kB` tức bằng 1,6 GB. Như mình đã nói tuy số dung lượng bộ nhớ trống bằng 1,5 GB tuy nhiên bạn có thể sử dụng nhiều hơn.

![](/images/image-kiem-tra-dung-luong-ram/pic6.png)
- (4) `Cached`: Đây là dung lượng bố nhớ sử dụng làm bộ lưu đệm. Vì bộ nhớ ram sẽ có tốc độ truy vấn đọc ghi rất cao nên hệ điều hành sử dụng lượng ram còn trống để cài thiện hiệu năng, tuy nhiên khi cần sử dụng bộ nhớ ram hệ điều hành sẽ tự động giải phóng bộ nhớ đệm.

Các cách kiểm tra tương tự
```
cat /proc/meminfo
less /proc/meminfo
```

## Một số câu lệnh kiểm tra thông dụng
Hiện thị dung lượng bộ nhớ có thể sử dụng bằng câu lệnh `free -m`, câu lệnh hiện thị dung lượng bộ nhớ dạng megabytes:
```
free -m
```

Kết quả

![](/images/image-kiem-tra-dung-luong-ram/pic7.png)


Hiện thị dung lượng bộ nhớ có thể sử dụng bằng câu lệnh `vmstat -s`
```
vmstat -s
```

Kết quả

![](/images/image-kiem-tra-dung-luong-ram/pic8.png)

## Giải thích lý do tại sao dung lượng bộ nhớ hiện có lại nhỏ hơn dung lượng bộ nhớ vật lý

![](/images/image-kiem-tra-dung-luong-ram/pic9.png)

Đến đây mình nghĩ một số bạn sẽ thắc mắc tại sao mình đăng ký gói Cloud VPS gói B với 2 GB RAM bộ nhớ nhưng khi kiểm tra thì hệ điều hành chỉ nhận 1.8 GB. Vậy có phải hệ điều hành đã nhận thiếu ram hoặc nhà cung cấp đã cung cấp thiếu bộ nhớ cho Cloud VPS?. 

Sau đây mình sẽ giải thích lý do. Thực ra, hệ điều hành Linux đã nhận đủ dung lượng bộ nhớ ram là 2 GB tuy nhiên trong quá trình khởi động hệ điều hành một phần ram đã bị chiếm dụng bởi nhân hệ thống (Kernel Linux).

Mình sẽ kiểm tra bằng câu lệnh
```
dmesg | grep -i memory
```

Kết quả

![](/images/image-kiem-tra-dung-luong-ram/pic10.png)

Nhìn vào (1) dễ thấy tổng dung lượng bộ nhớ hệ điều hành Linux đã nhận được thực sự là 2047 MB (`System RAM: 2047MB`). 

Tới (2), chúng ta thấy tuy tổng dung lượng là 2 GB (`2097124kB`) nhưng lại chỉ sử dụng được 1,8 GB (`1837732k`). 

Để ý tới (3), chúng ta sẽ lưu ý giá trị `259000k reserved`, giá thị thể hiện trong quá trình khởi động nhân hệ điều hành đã sử dụng 259 MB để thực hiện một số module đặc biệt. 

Nếu cộng giá trị `259000k` với dung lượng bộ nhớ có thể sử dụng `1837732k` chúng ta sẽ được giá trị gần bằng hoặc bằng dung lượng bộ nhớ vật lý của Cloud VPS. Tuy nhiên, bạn không phải lo lắng vì dung lượng bộ nhớ sử dụng cho nhân hệ điều hành sẽ được giải phóng một phần, trả lại cho bộ nhớ ram. Vì vậy tuy dung lượng bộ nhớ là 1.8 GB nhưng bạn có thể sử dụng nhiều hơn thế.

## Nguồn

https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/filesystems/proc.txt

https://serverfault.com/questions/219987/why-doesnt-the-value-in-proc-meminfo-seem-to-map-exactly-to-the-system-ram/219990#219990

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
