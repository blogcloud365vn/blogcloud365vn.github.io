---
title: Hướng dẫn cài đặt và tạo máy ảo với KVM trên centOS 7
categories:
  - Linux
description: Tài liệu hướng dẫn cài đặt KVM và tạo máy ảo KVM trên Centos 7 sử dụng dòng lệnh
author: niemdt
tags: [KVM, Linux, Centos]
type: Document
---

## Tổng quan

KVM (Kernel-based Virtual Machine) được biết đến là một cơ sở hạ tầng ảo hóa cho nhân Linux dành cho những CPU hỗ trợ công nghệ ảo hóa như Intel VT hoặc ADM-V.

Bài viết trước tôi đã giới thiệu bạn cách cài đặt KVM trên Ubuntu. Trong bài viết này tôi sẽ tiến hành cài đặt KVM trên máy chủ Centos 7 và sử dụng lệnh `virt-install` để tạo máy ảo.

## Chuẩn bị

- Một máy chạy hệ điều hành CentOS 7 với cấu hình tối thiểu 2 CPU, 2 GB RAM và 10 GB disk. Bạn có thể tham khảo [tại đây](https://cloud365.vn/#1#content-2) để mua một VPS phù hợp.
- Một file ISO của một hệ điều hành khác để cài máy ảo.

*Lưu ý* để cài được KVM thì cần phải được CPU hỗ trợ. Để kiểm tra xem CPU có hỗ trợ hay không sử dụng lệnh

`egrep -c "svm|vmx" /proc/cpuinfo`

Nếu kết quả trả về là khác `0` thì CPU có hỗ trợ.

## Cài đặt

### Cài KVM

Cài KVM và các gói phụ trợ liên quan

`yum install quemu-kvm libvirt-bin bridge-utils virt-manager`

Trong đó:

 * qemu-kvm: Phần phụ trợ cho KVM.
 * libvirt-bin: cung cấp libvirt mà bạn cần quản lý qemu và KVM bằng libvirt.
 * bridge-utils: chứa một tiện ích cần thiết để tạo và quản lý các thiết bị bridge.
 * virt-manager: cung cấp giao diện đồ họa để quản lý máy ảo.

Kiểm tra để chắc chắn rằng KVM đã được cài đặt

```
# lsmod | grep kvm
kvm_intel             204800  0
kvm                   593920  1 kvm_intel
irqbypass              16384  1 kvm
```

Đối với bản Minimal để dùng được công cụ đồ họa `virt-manager` người dùng phải cài đặt gói x-window bằng câu lệnh

`yum install "@X Window System" xorg-x11-xauth xorg-x11-fonts-* xorg-x11-utils -y`

Start dịch vụ libvirt và cho nó khởi động cùng hệ thống

```
systemctl start libvirtd
systemctl enable libvirtd
```

### Cài máy ảo

Có rất nhiều công cụ để tạo và quản lý máy ảo KVM. Bạn có thể sử dụng công cụ đồ họa `virt-manager` đã cài bên trên. Tham khảo cách tạo máy ảo bằng virt-manager [tại đây](/_posts/2019-01-24-huong-dan-cai-dat-centos7-kvm-bang-virt-manager.md)

Bài viết này tôi sẽ sử dụng câu lệnh `virt-install` để tạo máy ảo.

Cài `virt-install`

`yum install virt-install`

Cài package `virt-viewer` để hiển thị quá trình tạo VM 

`yum install virt-viewer`

Cài máy ảo

```
# virt-install \
> --name=Centos7-test \
> --vcpus=1 \
> --memory=512 \
> --cdrom=CentOS-7-x86_64-Minimal-1804.iso \
> --disk=/var/lib/libvirt/images/centos7-test,size=3 \
> --os-variant=rhel7 \
> --network bridge=virbr0

Starting install...    # bắt đầu cài đặt
Allocating 'centos7-test'                                                             | 3.0 GB  00:00:00     
Domain installation still in progress. Waiting for installation to complete.
```

**Những thông số đáng chú ý**

 * `--name` đặt tên cho máy ảo
 * `--vcpus` số CPU tạo cho máy ảo
 * `--memory` dung lượng RAM tạo cho máy ảo (đơn vị MiB)
 * `--cdrom` chỉ ra đường dẫn đến file ISO. Nếu boot bằng cách khác dùng option `--location` sau đó chỉ ra đường dẫn đến file (có thể là đường dẫn đến file trên internet)
 * `--disk` chỉ ra vị trí lưu disk của máy ảo. 
 * `size` chỉ ra dung lượng disk của máy ảo (đơn vị GiB)
 * `--os-variant` loại OS đang tạo. Option này có thể chỉ ra hoặc không nhưng nên sử dụng nó vì nó sẽ cải thiện hiệu năng của máy ảo sau này. Nếu bạn không biết HĐH bạn đang tạo thuộc loại nào bạn có thể tìm kiếm thông tin này bằng cách dùng lệnh `osinfo-query os`
 * `--network` loại mạng mà VM sử dụng.

Trên đây chỉ là một số option cở bản cho việc tạo VM. Bạn có thể tìm hiểu thêm bằng cách sử dụng lệnh `virt-install --help`

Đến đây ta tiến hành cài đặt OS như bình thường

![](/images/img-cai-kvm-ubuntu/3.0.png)

Kiểm tra vị trí lưu file disk của VM

```
[root@localhost ~]# ls /var/lib/libvirt/images/
centos7-test
```

## Tổng kết

Đến đây việc tạo một VM trên KVM đã thành công. Để hiểu rõ hơn về mô hình trong KVM bạn có thể tham khảo trong một số bài viết tiếp theo. Trong bài viết này chắc chắn sẽ còn nhiều thiếu sót rất mong được sự góp ý của các bạn.

Chúc bạn thành công!

Thực hiện bởi [cloud365.vn](https://cloud365.vn/)
