---
title: LDAP [Part 8] - Tích hợp LDAP với Check_MK
categories:
  - LDAP
description: Tích hợp LDAP với Check_MK
author: datpt
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 8
---

## Lời mở đầu.

Tiếp tục serie về LDAP và tích hợp LDAP. Trong bài viết này mình sẽ tiếp tục hướng dẫn cấu hình Check_MK - một công cụ để monitor mã nguồn mở xác thực với LDAP.

## Chuẩn bị.

- Một máy CentOS đã được [cài đặt Check_MK](https://blog.cloud365.vn/monitor/check_mk-part1-Tong-quan-ve-checkmk-va-vai-dat/#3-cai-%C4%91at-check_mk-tren-centos-7)

- [Cài đặt OpenLDAP](https://blog.cloud365.vn/ldap/LDAP-part-2-cai-dat-ldap-centos-7/){:target="_blank"}

- [Cài đặt phpLDAPadmin](https://blog.cloud365.vn/ldap/LDAP-part-3-cai-dat-php-ldap-admin/){:target="_blank"}

## Thao tác trên OpenLDAP.

Tạo cây thư mục như sau:

![ldap-32](/images/img-ldap-datpt/ldap-32.png)

## Thao tác trên Check_MK.

Login vào checkmk với tài khoản `cmkadmin` và thực hiện cấu hình.

Tại `Wato Configuration` chọn `Users`:

![ldap-33](/images/img-ldap-datpt/ldap-33.png)

Chọn `LDAP Connections`:

![ldap-34](/images/img-ldap-datpt/ldap-34.png)

Chọn `New Connection`:

![ldap-35](/images/img-ldap-datpt/ldap-35.png)

Cấu hình các thông tin như sau:

![ldap-36](/images/img-ldap-datpt/ldap-36.png)

![ldap-37](/images/img-ldap-datpt/ldap-37.png)

Lưu lại thông tin sau đó đăng xuất ra và đăng nhập với user trên LDAP:

![ldap-38](/images/img-ldap-datpt/ldap-38.png)

Kết quả như sau :

![ldap-39](/images/img-ldap-datpt/ldap-39.png)

## Tổng kết.

Như vậy trong bài viết này mình đã hướng dẫn cách tích hợp Check_MK để xác thực với LDAP. Chúc các bạn thực hiện thành công

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>