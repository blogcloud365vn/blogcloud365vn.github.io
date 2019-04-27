---
title: LDAP [Part 9] - Tích hợp LDAP với Graylog
categories:
  - LDAP
description: Tích hợp LDAP với Graylog
author: datpt
tags: [LDAP]
type: Document
set: tim-hieu-LDAP
set_order: 9
---

## Lời mở đầu.

Ở [bài viết trước](https://cloud365.vn/ldap/LDAP-part-8-Ldap-tich-hop-voi-checkmk/){:target="_blank"} mình đã hướng dẫn cách tích hợp Check_MK với LDAP. Ở trong bài viết này mình sẽ tiếp tục hướng dẫn cách tích hợp LDAP với Graylog

## Chuẩn bị.

- Một máy CentOS 7 đã được cài đặt Graylog

- [Cài đặt OpenLDAP](https://cloud365.vn/ldap/LDAP-part-2-cai-dat-ldap-centos-7/){:target="_blank"}

- [Cài đặt phpLDAPadmin](https://cloud365.vn/ldap/LDAP-part-3-cai-dat-php-ldap-admin/){:target="_blank"}

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


## Tổng kết.

Như vậy trong bài viết mình đã hướng dẫn các bạn cách tích hợp LDAP với Graylog. **Tạm thời** đây cũng sẽ là bài viết tích hợp LDAP với các phần mềm nguồn mở cuối cùng trong serie này của mình. Các phần mềm khác mình sẽ tiếp tục viết hướng dẫn tích hợp sau.

Trong các bài kế tiếp của serie, mình sẽ đề cập đến vấn đề **Failover** với OpenLDAP. Các mô hình LDAP triển khai với những hệ thống lớn.

Cảm ơn các bạn đã theo dõi.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>