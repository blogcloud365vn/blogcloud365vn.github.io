---
title: Hướng dẫn sử dụng tool ipscan trên windows
categories:
  - Linux, Beginner
description: Tài liệu download và sử dụng ipscan trên máy tính windows.
author: duydm
tags: [Beginner, Linux]
type: Document
---

Khi bạn quản lý rất nhiều IP có thể lên tới hàng nghìn địa chỉ IP, làm thế nào để bạn có thể biết tất cả các IP đó đang online một cách nhanh nhất. Bạn không thể thực hiện kiểm tra bằng tay hàng nghìn lần. Một sự lựa chọn đơn giản cho bài toán trên chính là sử dụng tool ipscan để thực hiện kiểm tra trạng thái của tất cả các IP mà bạn đang quản lý.

Dưới đây là các bước cài đặt và sử dụng tool `ipscan` cơ bản.

## 1. Tải tool

- Tải file cài đặt

Truy cập website: https://angryip.org/download/#windows

Lựa chon cho hệ điều hành windows và tải xuống

![](/images/img-ipscan/Screenshot_713.png)

- Chạy tool: Tool dạng file thực thi phải run mỗi lần sử dụng không cài đặt trực tiếp lên máy tính.

Chạy tool `ipscan-win64-3.5.5.exe`

![](/images/img-ipscan/Screenshot_715.png)

## 2. Thao tác sử dụng cơ bản

- Khi sử dụng tool có giao diện cơ bản như sau:

![](/images/img-ipscan/Screenshot_715.png)

- Ipscan cho phép bạn kiểm tra trạng thái ip theo các phương thức đầu vào như range (dải IP), random (ngẫu nhiên), text file (đầu vào là một file .txt)

![](/images/img-ipscan/Screenshot_716.png)

**Kiểm tra trạng thái IP của một range IP**

![](/images/img-ipscan/Screenshot_716.png)

Kết quả:

![](/images/img-ipscan/Screenshot_718.png)

![](/images/img-ipscan/Screenshot_719.png)

**Kiểm tra trạng thái IP random một số IP trong 1 subnet**

![](/images/img-ipscan/Screenshot_720.png)

**Kiểm tra trạng thái IP theo file .text đầu vào**

Định dạng file text đầu vào (Tool chỉ hỗ trợ định dạng .txt)

![](/images/img-ipscan/Screenshot_722.png)

![](/images/img-ipscan/Screenshot_721.png)

**Export kết quả ra file .txt**

![](/images/img-ipscan/Screenshot_723.png)

---
Thực hiện bởi [cloud365.vn](https://cloud365.vn/)