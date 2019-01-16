---
title: Hướng dẫn sử dụng phần mềm putty trên Windows
categories:
  - Windows
description: Tài liệu hướng dẫn cài đặt và sử dụng phần mềm putty trên Windows
author: duydm
tags: [Beginer, Windows, Linux]
type: Document
---

Putty là phần mềm miễn phí, được cài trên các hệ điều hành windows để truy cập vào các máy chủ linux hoặc các thiết bị mạng có hỗ trợ giao thức `SSH`.<br>

Bài viết này sẽ hướng dẫn bạn:
   Cài đặt phần mềm `putty` trên windows 10 64bit.<br>
   Hướng dẫn sử dụng `putty` để truy cập vào một máy linux (Centos hoặc Ubuntu ...) và một thiết bị mạng.<br>

Yêu cầu cần phải có là: Máy chủ đã cài `SSH server` hoặc thiết bị mạng đã được khai báo để truy cập thông qua SSH.

![](/images/img-puty/photo_2019-01-16_00-32-46.jpg)

## 1. Tải và cài đặt Putty

## Tải file cài đặt

Truy cập website: https://www.putty.org/

Lựa chon cho hệ điều hành windows và tải xuống. Chọn mục số 2 như hình dưới.

![](/images/img-puty/Screenshot_768.png)

Sau đó sẽ được điều hướng sang trang mới, tiếp tục chọn mục như ảnh bên dưới để download bộ cài của putty, máy sẽ hỏi bạn lưu ở đâu.

![](/images/img-puty/Screenshot_769.png)

### Cài đặt Putty

Bắt đầu cài đặt

Click vào file vừa tải về, sau đó chọn `Next`
![](/images/img-puty/Screenshot_770.png)

Để mặc định các gợi ý cài đặt và chọn `Next` tiếp theo.

![](/images/img-puty/Screenshot_771.png)

Chọn `Install` để cài đặt putty

![](/images/img-puty/Screenshot_772.png)

Sau đó chọn `Finish` để kết thúc việc cài đặt putty.

![](/images/img-puty/Screenshot_773.png)

## 2. Thao tác sử dụng cơ bản

### 2.1. Thực hiện ssh tới một server linux

Khởi động putty từ desktop hoặc từ thanh menu của windows. Ta sẽ có màn hình giao diện của putty. Sau đó bắt đầu nhập các thông số cần thiết như  ảnh bên dưới, bao gồm:

- Địa chỉ IP của máy chủ muốn remote
- Port của giao thức SSH, thường là `22` nếu ta không thay đổi gì.

![](/images/img-puty/Screenshot_774.png)

Sau đó chọn `Open`, ta sẽ có cửa sổ mới hiện ra, sau đó nhập user là `root` hoặc user khác nếu ta đã tạo trước đó. Trong ví dụ này là user `root`. 

![](/images/img-puty/Screenshot_775.png)

Nhập tiếp mật khẩu của user `root` để thực hiện đăng nhập vào máy chủ linux.


### 2.2. Thực hiện telnet tới một switch

Lựa chon giao thức telnet, nhập địa chỉ IP switch, port telnet của switch

![](/images/img-puty/Screenshot_776.png)

![](/images/img-puty/Screenshot_777.png)

---
Thực hiện bởi [cloud365.vn](https://cloud365.vn/)







