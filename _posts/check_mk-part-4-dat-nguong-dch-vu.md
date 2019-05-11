---
title: CHECKMK part 4 - Đặt ngưỡng cảnh báo cho dịch vụ.
categories:
  - Monitor
description: Tìm hiểu về omd-checkmk.
author: datpt
tags: [monitoring, Linux]
type: Document
set: Gioi-thieu-checkmk
set_order: 22
---

## Lời mở đầu

Ở bài trước mình đã hướng dẫn mọi người cách để cấu hình active check, ở bài này mình sẽ hướng dẫn cách để có thể cấu hình ngưỡng cảnh báo của dịch vụ. Mặc định thì checkmk đã có những ngưỡng cảnh báo cho từng dịch vụ, tuy nhiên những thông số đó chưa hẳn đã ổn đối với hệ thống của chúng ta vì thế chúng ta cần phải cấu hình lại ngưỡng cảnh báo của dịch vụ.

## Chuẩn bị

- Một máy CentOS 7 đã được cài đặt checkmk

- Một máy CentOS 7 đã cài checkmk agent và được thêm host trên checkmk.

## Thực hiện 

- Trên Web UI, chúng ta tìm đến `Views`, mở tab `Services` và chon `All services`.

- Trong ví dụ này, mình sẽ đặt ngưỡng cảnh báo cho service `Filesystem /` và rule ở `Folder main` với ngưỡng 70% là `Warning` và 80% là `Critical`.

- Tại `Filesystem /` chọn `Parameters for this service` :

![omd-28](/images/img-omd/omd-28.png)

- Chọn `Filesystems (used space and growth)`:

![omd-29](/images/img-omd/omd-29.png)

- Chọn `Create rule in folder`:

![omd-30](/images/img-omd/omd-30.png)

- Tìm đến tab `Parameters` và làm như các bước dưới đây :

![omd-31](/images/img-omd/omd-31.png)

- Sau đó nhấn `Save` để lưu lại:

![omd-32](/images/img-omd/omd-32.png)

- Lưu lại những thay đổi :

![omd-33](/images/img-omd/omd-33.png)

![omd-34](/images/img-omd/omd-34.png)

![omd-35](/images/img-omd/omd-35.png)

![omd-36](/images/img-omd/omd-36.png)

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>