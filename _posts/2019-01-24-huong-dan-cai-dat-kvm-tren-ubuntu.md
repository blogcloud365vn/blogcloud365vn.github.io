---
date: 2019-01-24
title: Hướng dẫn cài đặt KVM trên Ubuntu
categories:
  - Linux
description: Tài liệu hướng dẫn cài đặt KVM, tạo máy ảo KVM trên Ubuntu
author: niemdt
tags: [KVM, Linux, Ubuntu, Centos]
type: Document
---

**Tài liệu hướng dẫn cài đặt KVM, tạo máy ảo KVM dùng virt-manager trên Ubuntu**

## Tổng quan

KVM là một công nghệ hỗ trợ tạo máy ảo trên Linux. Nó giống như các phần mềm tạo máy ảo khác như VMware hay Virtual box nhưng KVM là một phần mềm mã nguồn mở và được tích hợp sẵn trong Linux. KVM thuộc loại ảo hóa full virtualization.

## Chuẩn bị

Một máy ubuntu cấu hình với ít nhất 2 CPU, 2 GB RAM và 15 GB disk.

Máy có card mạng để kết nối mạng cho VM ra ngoài.

*Lưu ý* để cài được KVM thì cần phải được CPU hỗ trợ. Để kiểm tra xem CPU có hỗ trợ hay không sử dụng lệnh

`egrep -c "svm|vmx" /proc/cpuinfo`

Nếu kết quả trả về là khác `0` thì CPU có hỗ trợ.

**Mô hình**

![](/images/img-cai-centos-kvm-ubuntu/a7.png)

## Cài đặt

## Cài KVM

Cài đặt KVM và công cụ quản trị `virt-manager`

`sudo apt-get install quemu-kvm libvirt-bin bridge-utils virt-manager`

Trong đó:

 * qemu-kvm: Phần phụ trợ cho KVM.
 * libvirt-bin: cung cấp libvirt mà bạn cần quản lý qemu và KVM bằng libvirt.
 * bridge-utils: chứa một tiện ích cần thiết để tạo và quản lý các thiết bị bridge.
 * virt-manager: cung cấp giao diện đồ họa để quản lý máy ảo.

Kiểm tra để chắc chắn rằng `KVM` đã được cài đặt

```
$ lsmod | grep kvm
kvm_intel             204800  0
kvm                   593920  1 kvm_intel
irqbypass              16384  1 kvm
```

**Tạo linux bridge cho VM kết nối mạng**

* Tạo linux bridge có tên `br0`

`sudo nmcli connection add type bridge autoconnect yes con-name br0 ifname br0`

* Add card `ens9` vào bridge

`sudo brctl addif br0 ens9`

* Xóa IP của card ens9

`sudo ifconfig ens9 0`

* Xin cấp IP cho bridge

`sudo dhclient br0`

Bạn có thể thấy `br0` đã nhận địa chỉ IP

![](/images/img-cai-centos-kvm-ubuntu/a8.png)

## Cài máy ảo

Đến bước này ta có thể tìm kiếm công cụ `virt-manager` trong kho ứng dụng

![](/images/img-cai-kvm-ubuntu/1.png)

Hoặc có thể gõ trực tiếp câu lệnh `virt-manager` vào terminal để gọi trực tiếp 

![](/images/img-cai-kvm-ubuntu/2.png)

Click vào `Create a new virtual machine`

![](/images/img-cai-centos-kvm-ubuntu/2.png)

Chọn cách để boot hệ điều hành

![](/images/img-cai-centos-kvm-ubuntu/3.png)

Trong đó:

 * Local install media: cài hệ điều hành từ một file ISO hoặc đĩa CDROM có sẵn trên maý.
 * Network install: lấy file ISO từ trên mạng hoặc một máy ở xa.
 * Network boot: cài hệ điều hành thông qua mạng.
 * Import existing disk image: tạo máy thông qua một file image có sẵn.

Ở ví dụ này tôi sẽ tạo máy thông qua một file ISO có sẵn trong máy.

Chỉ ra đường dẫn đến file ISO trên máy

![](/images/img-cai-centos-kvm-ubuntu/4.png)

Chỉ ra dung lượng RAM (tính bằng MiB) và số CPU cho VM

![](/images/img-cai-centos-kvm-ubuntu/5.png)

Chọn dung lượng disk cho VM (đơn vị GiB).

![](/images/img-cai-centos-kvm-ubuntu/6.png)

Trong đó: 

 * Create a disk image for the virtual machine: lựa chọn này file image của VM sẽ nằm trong thư mục `/var/lib/libvirt/images`
 * Select or create custom storage: lựa chọn này sẽ sau đó phải chỉ ra vị trí lưu file image cho VM.

Xem lại thông số trước khi bắt đầu cài đặt. Chọn kết nối mạng đến bridge vừa tạo.

![](/images/img-cai-centos-kvm-ubuntu/a4.png)

Trong đó: 
 * Name: để đặt tên cho VM
 * Customize configuration before install: tích và mục này nếu muốn nay đổi gì đó trước khi cài đặt.

Kiểm tra một lần nữa các thông số. Sau đó click vào `Begin installation`

![](/images/img-cai-centos-kvm-ubuntu/a5.png)

Tiến hành cài OS cho máy ảo

![](/images/img-cai-centos-kvm-ubuntu/9.png)

Sau khi cài xong ta login vào và kiểm tra IP. 

![](/images/img-cai-centos-kvm-ubuntu/a6.png)


## Tổng kết

Đến đây bạn đã cài đặt KVM và tạo thành công máy ảo trên KVM bằng công cụ đồ họa virt-manager. Bài viết chắc chắn sẽ còn nhiều thiếu sót rất mong được sự góp ý của các bạn.

Chúc bạn thành công!

Thực hiện bởi [cloud365.vn](https://cloud365.vn/){:target="_blank"}
