---
title: LDAP part 8 - Tích hợp LDAP với Graylog
categories:
  - LDAP
description: Tìm hiểu về LDAP.
author: datpt
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 22
---

## Lời mở đầu.

Hôm nay mình sẽ hướng dẫn mọi người cách để tích hợp LDAP với graylog, một hệ thống thu thập log tập trung.

## Chuẩn bị.

- Một máy CentOS 7 đã được cài đặt Graylog

- Một máy CentOS đã được [cài đặt OpenLDAP]()

## Thao tác trên OpenLDAP.

Tạo cây thư mục user như sau:

![ldap-32](/images/img-ldap-datpt/ldap-32.png)

Tạo cây thư mục cho group phân quyền như sau :

![ldap-60](/images/img-ldap-datpt/ldap-60.png)

Lưu ý, user muốn được phân quyền trên graylog thì cần có `User ID` thuộc trường `memberUid` của group như sau, nếu chưa có thì chúng ta thêm vào :

![ldap-61](/images/img-ldap-datpt/ldap-61.png)

## Thao tác trên Graylog.

Login vào Graylog với tài khoản admin và thực hiện các bước sau :

- Tại tab `Administrator` chọn `Edit profile`:

![ldap-51](/images/img-ldap-datpt/ldap-51.png)

- Tại đây, chọn `LDAP/Active Directory`:

![ldap-52](/images/img-ldap-datpt/ldap-52.png)

- Điền các thông tin như sau, rồi sau đó test connection đến server LDAP :

![ldap-53](/images/img-ldap-datpt/ldap-53.png)

- Tiếp tục phần `User mapping`:

![ldap-54](/images/img-ldap-datpt/ldap-54.png)

- Phần `Group mapping`:

![ldap-55](/images/img-ldap-datpt/ldap-55.png)

- Sau đó chọn `Save LDAP settings`:

![ldap-56](/images/img-ldap-datpt/ldap-56.png)

- Sau khi lưu lại các cấu hình thành công, kéo lên trên cùng chọn `LDAP Group Mapping`:

![ldap-57](/images/img-ldap-datpt/ldap-57.png)

- Phân quyền user thông qua các group trên LDAP sau đó chọn Save:

![ldap-58](/images/img-ldap-datpt/ldap-58.png)

- Đăng nhập lại bằng user từ LDAP, kết quả như sau:

![ldap-59](/images/img-ldap-datpt/ldap-59.png)

Chúc mọi người thành công !



## Tổng kết.

Cảm ơn mọi người đã đến với Cloud365, để chất lượng những bài viết được tốt hơn rất mong có những đóng góp từ mọi người.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>