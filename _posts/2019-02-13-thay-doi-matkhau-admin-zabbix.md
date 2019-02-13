---
date: 2019-01-13
title: Hướng dẫn thay đổi mật khẩu admin zabbix
categories:
  - Monitor
description: Hướng dẫn thay đổi mật khẩu tài khoản admin zabbix đảm bảo an toàn cho hệ thống.
author: duydm
tags: [Zabbix]
type: Document
---

Ở bài hướng dẫn <a href="https://blog.cloud365.vn/monitor/cai-dat-zabbix-4-lts-tren-centos7/" target="_blank">cài đặt</a> `Zabbix 4.0` LTS trên CentOS 7 đã giới thiệu cho các bạn mô hình triển khai, các bước cài đặt và login vào giao diện web dashboard của zabbix. Sau khi cài đặt thành công zabbix cung cấp cho bạn một tài khoản mặc định `Admin\zabbix` có quyền cao nhất Administrator trên hệ thống zabbix. Điều đầu tiên để hệ thống được bảo mật an toàn phải đổi mật khẩu của tài khoản Admin. Bài viết dưới đây sẽ hướng dẫn bạn các bước thực hiện.

## 1. Login dashboard zabbix

Truy cập địa chỉ trên trình duyệt: `http://ip_zabbix_server`

![](/images/img-change-pass-admin-zabbix/Screenshot_953.png)

Sử dụng tài khoản default để login zabbix server `Admin\zabbix`

![](/images/img-change-pass-admin-zabbix/Screenshot_952.png)

## 2. Thay đổi mật khẩu Admin

Truy cập tab `Administrator` -> `User`

![](/images/img-change-pass-admin-zabbix/Screenshot_954.png)

Click user `Admin` muốn thay đổi mật khẩu

![](/images/img-change-pass-admin-zabbix/Screenshot_955.png)

Click `Change password`

![](/images/img-change-pass-admin-zabbix/Screenshot_956.png)

Nhập mật khẩu mới -> Click `Update`

![](/images/img-change-pass-admin-zabbix/Screenshot_957.png)

**Lưu ý:** Để đảm bảo an toàn khuyến cáo bạn nên sử dụng mật khẩu mạnh:

```
- Có độ dài từ 08 ký tự trở lên
- Có chứa ít nhất một ký tự viết hoa
- Có chứa ít nhất một ký tự số
- Có chứa ít nhất một ký tự đặc biệt.
```

Ví dụ: `5_AA#jBsk^gfwSNb`, `jUu+4s2=*2cyngPY`, `*g7pz%9D38VM8#Jg`...

Update mật khẩu tài khoản Admin thành công.

![](/images/img-change-pass-admin-zabbix/Screenshot_959.png)

Với những hướng dẫn cơ bản trên hy vọng giúp bạn thực hiện đảm bảo an toàn cho hệ thống monitor zabbix.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>