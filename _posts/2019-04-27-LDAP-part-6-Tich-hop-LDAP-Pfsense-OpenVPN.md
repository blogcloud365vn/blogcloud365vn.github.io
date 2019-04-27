---
title: LDAP [Part 6] - OpenVPN Pfsense đối với user được tích hợp từ LDAP
categories:
  - LDAP
description: OpenVPN Pfsense đối với user được tích hợp từ LDAP
author: datpt
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 6
---

## Lời mở đầu.

Một vấn đề chúng ta gặp phải khi tích hợp LDAP với hệ thống pfsense đó là khi chúng ta đang sử dụng VPN trên Pfsense, như vậy bằng cách nào những user trên LDAP có thể sử dụng để VPN thông qua Pfsense, ở bài viết này mình sẽ hướng dẫn mọi người cách sử dụng user trên LDAP để VPN thông qua pfsense.

## Chuẩn bị.

- [Cài đặt OpenLDAP](https://cloud365.vn/ldap/LDAP-part-2-cai-dat-ldap-centos-7/){:target="_blank"}

- Một Pfsense server

- Đã [tích hợp LDAP xác thực qua Pfsense](https://cloud365.vn/ldap/LDAP-part-6-Tich-hop-LDAP-Pfsense-OpenVPN/){:target="_blank"}

- Đã cấu hình rules cho OpenVPN và cài client export của OpenVPN.

## Mô hình

![ldap-19](/images/img-ldap-datpt/ldap-19.png)

## Cấu hình trên pfsense

Tại tab `System/Certificate Manager/CA`, chọn `Add` để thêm một CA mới, CA này sẽ xác thực tất cả các certificate của server VPN và user VPN khi kết nối tới PFSense:

![ldap-20](/images/img-ldap-datpt/ldap-20.png)

Điền đầy đủ các thông tin như sau rồi chọn `Save` :

![ldap-21](/images/img-ldap-datpt/ldap-21.png)

Tại tab `System/Certificate Manager/Certificate`, tạo certificate cho server VPN :

![ldap-22](/images/img-ldap-datpt/ldap-22.png)

Điền thông tin như sau :

![ldap-23](/images/img-ldap-datpt/ldap-23.png)

![ldap-24](/images/img-ldap-datpt/ldap-24.png)

Tại tab `VPN/OpenVPN/Servers`, click `Add` để tạo VPN server:

![ldap-25](/images/img-ldap-datpt/ldap-25.png)

![ldap-26](/images/img-ldap-datpt/ldap-26.png)

![ldap-27](/images/img-ldap-datpt/ldap-27.png)

Sau đó `Save` lại.

Tiếp tục tạo cert cho user trên LDAP. Tại tab `System/Certificate Manager/Certificate`, tạo certificate cho user trên LDAP , chọn `Add/Sign`:

![ldap-28](/images/img-ldap-datpt/ldap-28.png)

Điền các thông tin :

![ldap-29](/images/img-ldap-datpt/ldap-29.png)

![ldap-30](/images/img-ldap-datpt/ldap-30.png)

Trong đó :

- Descriptive name và Common Name là `cn` trên LDAP.

Vào tab `VPN/OPENVPN/Client Export` và tải về để kiểm tra

![ldap-31](/images/img-ldap-datpt/ldap-31.png)

## Tổng kết.

Như vậy trong bài viết này mình đã hướng dẫn cách xác thực OpenVPN của Pfsense với tài khoản xác thực từ LDAP. Chúc các bạn thực hiện thành công.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>