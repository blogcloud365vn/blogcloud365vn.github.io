---
title: Hướng dẫn cài đặt kimchi trên CentOS 7
categories:
  - Linux
description: Tài liệu hướng dẫn cài đặt kimchi trên CentOS 7
author: niemdt
tags: [KVM, Linux, CentOS]
type: Document
---

## Tổng quan

Kimchi có chức năng tượng tự như webvirt được dùng để quản lý VM trong KVM nhưng có nhược điểm không quản lý tập trung được nhiều KVM host so với webvirt và nó được cài trực tiếp lên KVM host đó.

## Chuẩn bị

- Một máy cài hệ điều hành CentOS 7 có cấu hình tối thiểu 2 CPU, 1.5G RAM và 10G disk và có 1 card mạng.

- CPU của máy có hỗ trợ. Để kiểm tra xem CPU có hỗ trợ hay không sử dụng lệnh

```
`egrep -c "svm|vmx" /proc/cpuinfo`
```

Nếu kết quả trả về là khác `0` thì CPU có hỗ trợ.

## Cài đặt

**Update hệ thống và cài đặt các goí cần thiết**

```
yum -y update

yum -y install epel-release deltarpm chronyd wget 

yum makecache

yum install libvirt-python libvirt libvirt-daemon-config-network qemu-kvm python-ethtool sos \

python-ipaddr nfs-utils iscsi-initiator-utils pyparted python-libguestfs libguestfs-tools novnc \

spice-html5 python-configobj python-magic python-paramiko python-pillow virt-top

systemctl enable chronyd

systemctl restart chronyd
```

**Cài đặt Kimchi và các gói liên quan**

```
yum -y install http://kimchi-project.github.io/gingerbase/downloads/latest/ginger-base.el7.centos.noarch.rpm \

http://kimchi-project.github.io/ginger/downloads/latest/ginger.el7.centos.noarch.rpm \

https://github.com/kimchi-project/wok/releases/download/2.5.0/wok-2.5.0-0.el7.centos.noarch.rpm \

https://github.com/kimchi-project/kimchi/releases/download/2.5.0/kimchi-2.5.0-0.el7.centos.noarch.rpm
```

**Restart các service và cho phép khởi động cùng hệ thống**

```
systemctl restart wokd nginx

systemctl enable wokd nginx
```

**Tắt firewalld và selinux**

Đây là môi trường lap nên tôi tiến hành tắt firewalld và selinux. Nếu bạn khống muốn bạn có thể mở port 8001.

Tắt firewalld

```
systemctl stop firewalld

systemctl disable fireawalld
```

Disable SElinux

```
setenforce 0
```

Với lệnh này chỉ disable cho đến khi reboot lại server. Nếu muốn bạn có thể vào file `/etc/selinux/config` để disbale.

**Đăng nhập**

Bây giờ bạn mở trình duyệt web và truy và server với cú pháp

```
https://IP-server:8001
```

*Lưu ý:* Nếu không sử dụng `https` bạn sẽ gặp phải lỗi như sau

![](/images/img-kimchi/1.png)

Lần đầu tiên đăng nhập màn hình sẽ hiện lên như sau. Bạn click vào `ADVANCE`

![](/images/img-kimchi/2.png)

Click vào `Proceed to IP (usafe)` ở dòng cuối cũng

![](/images/img-kimchi/3.png)

Màn hình đăng nhập sẽ như sau

![](/images/img-kimchi/4.png)

## Sử dụng

Để đăng nhập vào bạn sử dụng user name và password dùng để nhập vào KVM host để đăng nhạp vào Kimchi. Sau khi đăng nhập vào bạn sẽ thấy giao diện như sau

![](/images/img-kimchi/a1.png)

Để tạo máy ảo trước tiên bạn cần tạo templates. Click vào `Virtualization` chọn `Templates` và `Add template`

![](/images/img-kimchi/a2.png)

Sau đó điền tên của template. Bạn có điền đường dẫn đến file ISO của HĐH vào `File Path` hoặc chọn ở bên dưới. Nếu bạn chọn bên dưới mà trong máy của bạn ko có file đó thì nó sẽ download file đó về từ internet.

![](/images/img-kimchi/a3.png)

Tiếp theo click vào `Guests` và chọn `Add Guest`

![](/images/img-kimchi/a4.png)

Điền tên của VM và chọn template

![](/images/img-kimchi/a5.png)

Để start VM bạn di chuột vào `Actions` và chọn `Start`

![](/images/img-kimchi/a6.png)

Để hiển thị màn hình của VM bạn chọn `View Console`

![](/images/img-kimchi/a7.png)

## Tổng kết

Đến đây bạn đã cài đặt, đăng nhập và tạo VM thành công bằng công cụ quản lý máy ảo KVM Kimchi trên CentOS 7.

Chúc bạn thành công!

**Tài liệu tham khảo**

https://github.com/MinhKMA/meditech-thuctap/blob/master/MinhNV/KVM/docs/th%E1%BB%B1c%20h%C3%A0nh/kimchi.md

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>