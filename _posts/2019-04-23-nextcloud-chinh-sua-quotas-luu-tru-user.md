---
title: NextCloud - Chỉnh sửa Quotas lưu trữ của User
categories:
  - NextCloud
description: NextCloud - Chỉnh sửa Quotas lưu trữ của User
author: canhdx
tags: [Nextcloud]
type: Document
set: NextCloud
set_order: 19
---

## NextCloud Quotas
Đối với các tài khoản được cấp việc giới hạn dung lượng sử dụng của các user là bài toán vô cùng cần thiết đối với admin. Bài viết sẽ hướng dẫn các bạn cách cấu hình mức Quotas cần thiết đối với mỗi User.

Mặc định NextCloud cung cấp 5 định mức mặc định sử dụng dữ liệu cho các User
<p align="center">
<img src="/images/img-nextcloud/nextcloud-072.png">
</p>

- `Default quotas`: Giá trị mặc định mà mỗi user được tạo ra được cấu hình. 
- `Unlimited`: Giá trị này cho phép user sử dụng tối đa tài nguyên lưu trữ của hệ thống 
- `1GB`: Giới hạn lưu trữ 1GB 
- `5GB`: Giới hạn lưu trữ 5GB 
- `10GB`: Giới hạn lưu trữ 10GB 

## Chỉnh sửa Quotas

Điều chỉnh `Default quotas`. Giá trị này mặc định sẽ cấu hình cho các user tạo mới. 

Truy cập `Settings`
<p align="center">
<img src="/images/img-nextcloud/nextcloud-073.png">
</p>

Tại ô `Default quotas` chúng ta nhập dung lượng default mà mỗi user có thể có được khi khởi tạo tài khoản 
<p align="center">
<img src="/images/img-nextcloud/nextcloud-074.png">
</p>

Bấm `Enter` để lưu giá trị thay đổi
<p align="center">
<img src="/images/img-nextcloud/nextcloud-075.png">
</p>

> Bây giờ mỗi khi chúng ta chọn giá trị Default Quotas thì mặc định sẽ chuyển quotas thành `7GB`

Tạo mới User 
<p align="center">
<img src="/images/img-nextcloud/nextcloud-076.png">
</p>

Điền các thông tin đầy đủ để `Default Quotas`và bấm xác nhận
<p align="center">
<img src="/images/img-nextcloud/nextcloud-077.png">
</p>

Logout tài khoản admin và login bằng tài khoản vừa tạo. Chúng ta sẽ thấy là dung lượng mặc định của User là 7GB 

<p align="center">
<img src="/images/img-nextcloud/nextcloud-078.png">
</p>

Quay lại login bằng tài khoản admin tiến hành chỉnh sửa lại Quotas của user này 

Chọn mục Quotas
<p align="center">
<img src="/images/img-nextcloud/nextcloud-079.png">
</p>

Tại đây chúng ta có thể chọn 1 giá trị có sẵn bằng cách click vào giá trị đó . Ở đây mình chọn 1GB 

Quay lại tài khoản `canhdx` và kiểm tra 
<p align="center">
<img src="/images/img-nextcloud/nextcloud-080.png">
</p>

Nếu chúng ta muốn set một giá trị khác thì sao. Đơn giản chúng ta gõ giá trị trực tiếp vào ô quotas theo định dạng quy chuẩn (<giá trị> MB,GB,TB... ). Ở đây mình set là 4GB
<p align="center">
<img src="/images/img-nextcloud/nextcloud-081.png">
</p>

Bấm `Enter` để lưu giá trị
<p align="center">
<img src="/images/img-nextcloud/nextcloud-082.png">
</p>

Quay lại tài khoản `canhdx` và kiểm tra 
<p align="center">
<img src="/images/img-nextcloud/nextcloud-083.png">
</p>

## Tổng kết

Bài viết đã hướng dẫn các bạn chi tiết về việc set quotas cho user. Hy vọng các bạn có thể áp dụng được trong mô hình của chính bạn. 

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
