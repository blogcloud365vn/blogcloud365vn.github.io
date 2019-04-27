---
title: LDAP part 6 - Tích hợp LDAP với PHPIPAM
categories:
  - LDAP
description: Tìm hiểu về LDAP.
author: datpt
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 24
---

## Lời mở đầu.

Hôm nay mình sẽ hướng dẫn mọi người cách để tích hợp LDAP với PHPIPAM, một phần mềm mã nguồn quản dùng để quản lý địa chỉ IP.

## Chuẩn bị.

- Một máy CentOS 7 đã được cài đặt PHPIPAM

- Một mát CentOS 7 đã được [cài đặt LDAP]()

## Trên máy chủ LDAP.

- Tạo một cây thư mục như sau :

![ldap-40](/images/img-ldap-datpt/ldap-40.png)

## Trên máy chủ PHPIPAM.

- Tại tab `Adminstration` Chọn `Authentication methods`:

![ldap-41](/images/img-ldap-datpt/ldap-41.png)

- Tại đây tiếp tục chọn `Create new LDAP authentication`:

![ldap-42](/images/img-ldap-datpt/ldap-42.png)

- Điền đầy đủ các thông tin sau đó chọn `Add`:

![ldap-43](/images/img-ldap-datpt/ldap-43.png)

- Để kiểm tra kết nối của máy chủ IPAM đến máy chủ LDAP , chúng ta chọn tùy chọn sau để kiểm tra :

![ldap-44](/images/img-ldap-datpt/ldap-44.png)

- Nếu thành công, kết quả như sau :

![ldap-45](/images/img-ldap-datpt/ldap-45.png)

- Để thêm user xác thực từ máy chủ LDAP chọn tab `Adminstration` rồi chọn `Users`:

![ldap-46](/images/img-ldap-datpt/ldap-46.png)

- Tại đây, chọn `Create User`:

![ldap-47](/images/img-ldap-datpt/ldap-47.png)

- Điền các thông tin như map với LDAP như sau :

![ldap-48](/images/img-ldap-datpt/ldap-48.png)

- Đăng xuất và thử đăng nhập lại với user vừa thêm với mật khẩu là mật khẩu được lưu trên LDAP , kết quả như sau :

![ldap-49](/images/img-ldap-datpt/ldap-49.png)

- Kiểm tra user từ LDAP :

![ldap-50](/images/img-ldap-datpt/ldap-50.png)


Chúc mọi người thành công !

## Tổng kết.

Cảm ơn mọi người đã đến với Cloud365, để chất lượng những bài viết được tốt hơn rất mong có những đóng góp từ mọi người.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>