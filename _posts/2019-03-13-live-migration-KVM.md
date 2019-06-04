---
title: Hướng dẫn live migration trên KVM
categories:
  - Linux
description: Tài liệu hướng dẫn live migration trên KVM
author: niemdt
tags: [KVM, Linux, CentOS]
type: Document
---

## Tổng quan

Trong quá trình vận hành để phục vụ cho việc bảo trì và nâng cấp hệ thống chúng ta cần chuyển các VM từ host này sang host khác. Với các VM đang chạy các ứng dụng quan trọng chúng ta không thể tắt nó đi trong quá trình chuyển. Trên KVM việc `live migrate` sẽ đảm bảo được các yêu cầu này.

## Chuẩn bị

Chuẩn bị ba máy cài đặt hệ điều hành CentOS 7 trong đó một máy dùng để cài đặt NFS dùng làm máy lưu file disk của VM và 2 máy cài đặt KVM.

Cấu hình

* NFS server 
    * 1 CPU
    * 1G RAM
    * 10G disk
* Host KVM
    * 2 CPU
    * 1.5G RAM
    * 10G disk

Ba máy này cần có card mạng và có thể kết nối được với nhau

Đây là môi trường lap nên tôi tắt firewalld và selinux trên cả 3 máy. Để tắt sử dụng 3 câu lệnh sau trên cả ba máy:

```
systemctl stop firewalld 

setenforce 0
```

**Mô hình như sau**

![](/images/img-live-migrate/3.png)

## Cài đặt

**Cấu hình phân dải tên miền**

Để có thể live migrate giữa 2 KVM host thì 2 máy này cần biết tên miền của nhau. Bạn có thể cấu hình dịch vụ DNS phân dải tên miền cho các 2 máy này. Đây là mô hình lap có ít máy nên tôi sẽ cấu hình luôn trong file `/etc/hosts`

Thực hiện các thao tác sau trên lần lượt các máy

#### Trên KVM host 192.168.122.118

Sửa tên máy trong file `/etc/hostname`. Máy này tôi để tên là `test1`

```
test1
```

Chỉ ra tên miền và địa chỉ của máy KVM host còn lại trong file `/etc/hosts`. Thêm dòng sau vào cuối file. Chú ý đổi đúng địa chỉ IP máy KVM host còn lại của bạn

```
192.168.122.219  test2.local test2
```

Sau đó reboot lại máy

#### Trên KVM host 192.168.122.219

Ta cũng thực hiện tương tự như bên trên

Sửa file `/etc/hostname`

```
test2
```

Bổ sung vào file `/etc/hosts` dòng sau. Chú ý thay đổi địa chỉ IP thích hợp

```
192.168.122.118  test1.local test1
```

Sau đó reboot lại máy.

**Cài đặt NFS**

#### Trên NFS server

Để cài đặt dùng lệnh 

```
yum install nfs-utils nfs-utils-lib
```

Tạo một thư mục share

```
mkdir storage
```

Chia sẻ thư mực này với các máy KVM host bằng cách ghi các thông tin như sau vào trong file `/etc/exports`

```
/root/storage 192.168.122.219/24(rw,sync,no_root_squash)
/root/storage 192.168.122.118/24(rw,sync,no_root_squash)
```

Địa chỉ IP bên trên là địa chỉ IP của 2 máy KVM host

Sau đó cập nhật file này bằng cách chạy lệnh

```
exportfs -a
```

Khởi động dịch vụ nfs

```
service nfs start
```

#### Trên máy KVM host

Trên cả 2 máy đều thực hiện các câu lệnh sau.

Cài đặt NFS

```
yum install nfs-utils nfs-utils-lib
```

Tạo thư mục

```
mkdir storage
```

Mount với thư mục đã share

```
mount 192.168.122.229:/root/storage/  storage
```

Lưu ý mỗi khi reboot lại máy ta cần mount lại các thư mục này. Nếu không muốn bạn muont nó bằng cách sửa file `/etc/fstab`

**Cài đặt KVM**

Thực hiện cài đặt KVM trên cả 2 máy KVM host. Để cài đặt KVM bạn tham khảo <a href="https://blog.cloud365.vn/linux/huong-dan-cai-dat-kvm-tren-centos7/#cai-%C4%91at" target="_blank">tại đây</a>.

Khi cài đặt VM ta cần lưu file disk của VM vào thư mục đã mount với thư mục được share của NFS server. Khi cài máy ảo xong ta cần thêm thông tin sau vào trong file `xml` của VM bằng cách dùng lệnh 

```
virsh edit tên-VM
```

![](/images/img-live-migrate/1.0.png)

Thêm vào cache='none' để tránh migrate tránh mất dữ liệu. Sau đó reboot lại VM. Các bước này nên thực hiện ngay sau cài VM kể cả bạn chưa có ý định live migrate ngay lúc này bởi vì khi cần migrate có thể thực hiện được luôn mà không cần phải reboot VM khi đã cài các ứng dụng lên.

**Kết nối qemu giữa hai KVM host**

Để có thể live migrate giữa hai host thì hai host này cần phải kết nối được với nhau. Để làm được việc này ta thực hiện các bước sau ở trên cả hai máy host KVM.

```
sed -i 's/#listen_tls = 0/listen_tls = 0/g' /etc/libvirt/libvirtd.conf 
sed -i 's/#listen_tcp = 1/listen_tcp = 1/g' /etc/libvirt/libvirtd.conf
sed -i 's/#tcp_port = "16509"/tcp_port = "16509"/g' /etc/libvirt/libvirtd.conf
sed -i 's/#listen_addr = "192.168.0.1"/listen_addr = "0.0.0.0"/g' /etc/libvirt/libvirtd.conf
sed -i 's/#auth_tcp = "sasl"/auth_tcp = "none"/g' /etc/libvirt/libvirtd.conf
sed -i 's/#LIBVIRTD_ARGS="--listen"/LIBVIRTD_ARGS="--listen"/g' /etc/sysconfig/libvirtd
```

Restart lại libvirtd trên cả hai máy

```
systemctl restart libvirtd
```

**Migrate**

Công đoạn cuối cùng là tiến hành migrate. Trước tiên kiểm tra VM trên máy 192.168.122.219

```
[root@test2 ~]# virsh list --all

 Id    Name                           State
----------------------------------------------------
 5     demo1                          running
```

Ta cũng có thể thấy trên KVM host 192.168.122.118 hiện không có VM nào

```
[root@test1 ~]# virsh list --all
 Id    Name                           State
----------------------------------------------------
```

Trước khi migrate tôi vào trong VM `demo1` thực hiện lệnh `ping 8.8.8.8`

![](/images/img-live-migrate/2.png)

Tiến hành migrate từ KVM host 192.168.122.219 sang host 192.168.122.118

```
virsh migrate --live demo1 qemu+tcp://192.168.122.118/system
```

Kiểm tra trên host 192.168.122.118 lúc này đã thấy VM `demo1` đang chạy

```
[root@test1 qemu]# virsh list --all
 Id    Name                           State
----------------------------------------------------
 5     demo1                          running
```

Ta thấy trong qúa trình live migrate lệnh ping vẫn hoạt động

![](/images/img-live-migrate/1.1.png)

## Tổng kết

Đến đây bạn đã có thể thiết lập để live migrate thành công. Trong bài viết chắc chắn còn nhiều thiếu sót rất mong được sự góp ý của các bạn.

Chúc bạn thành công!

**Tài liệu tham khảo**

https://www.server-world.info/en/note?os=CentOS_7&p=kvm&f=8

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>