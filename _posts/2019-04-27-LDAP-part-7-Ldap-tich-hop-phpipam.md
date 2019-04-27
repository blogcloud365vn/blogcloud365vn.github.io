---
title: LDAP [Part 7] - Tích hợp LDAP với phpIPAM
categories:
  - LDAP
description: Tích hợp LDAP với phpIPAM
author: datpt
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 7
---

## Lời mở đầu.

Để tiếp tục serie LDAP, tại bài viết này mình sẽ hướng dẫn các bạn cách tích hợp LDAP với phpIPAM - một công cụ quản lý IP.

## Chuẩn bị.

- Một máy CentOS 7 đã được cài đặt PHPIPAM

- [Cài đặt OpenLDAP](https://cloud365.vn/ldap/LDAP-part-2-cai-dat-ldap-centos-7/){:target="_blank"}

- [Cài đặt phpLDAPadmin](https://cloud365.vn/ldap/LDAP-part-3-cai-dat-php-ldap-admin/){:target="_blank"}

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


## Tổng kết.

Như vậy trong bài viết này mình đã hướng dẫn cách cấu hình phpIPAM xác thực với LDAP. Chúc các bạn thực hiện thành công.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>