---
date: 2019-03-07
title: "Lý do Cloud VPS Linux thiếu RAM"
categories:
  - Linux
description: Lý do Cloud VPS Linux thiếu RAM
author: thanhnb
tags: [Linux, CentOS7]
type: Document
---

## Lý do dung lượng RAM Cloud VPS nhận thiếu

![](/images/image-kiem-tra-dung-luong-ram/pic9.png)

Mình chắc chắn một số bạn thường thắc mắc tại sao Cloud VPS Linux luôn có dung lượng RAM nhỏ hơn gói đề ra của nhà cung cấp. Vậy có phải hệ điều hành đã nhận thiếu RAM hoặc nhà cung cấp đã cung cấp thiếu bộ nhớ cho Cloud VPS?.

Sau đây mình sẽ giải thích lý do. Thực ra, hệ điều hành Linux đã nhận đủ dung lượng bộ nhớ RAM là 2 GB tuy nhiên trong quá trình khởi động hệ điều hành một phần RAM đã bị chiếm dụng bởi nhân hệ thống (Kernel Linux).

Mình sẽ kiểm tra bằng câu lệnh
```
dmesg | grep -i memory
```

Kết quả

![](/images/image-kiem-tra-dung-luong-ram/pic10.png)

Nhìn vào (1) dễ thấy tổng dung lượng bộ nhớ hệ điều hành Linux đã nhận được thực sự là 2047 MB (`System RAM: 2047MB`). 

Tới (2), chúng ta thấy tuy tổng dung lượng là 2 GB (`2097124kB`) nhưng lại chỉ sử dụng được 1,8 GB (`1837732k`). 

Để ý tới (3), chúng ta sẽ lưu ý giá trị `259000k reserved`, giá thị thể hiện trong quá trình khởi động nhân hệ điều hành đã sử dụng 259 MB để thực hiện một số module đặc biệt. 

Nếu cộng giá trị `259000k` với dung lượng bộ nhớ có thể sử dụng `1837732k` chúng ta sẽ được giá trị gần bằng hoặc bằng dung lượng bộ nhớ vật lý của Cloud VPS. Tuy nhiên, bạn không phải lo lắng vì dung lượng bộ nhớ sử dụng cho nhân hệ điều hành sẽ được giải phóng một phần, trả lại cho bộ nhớ RAM. Vì vậy tuy dung lượng bộ nhớ là 1.8 GB nhưng bạn có thể sử dụng nhiều hơn thế.

## Nguồn

https://serverfault.com/questions/219987/why-doesnt-the-value-in-proc-meminfo-seem-to-map-exactly-to-the-system-ram/219990#219990

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
