---
date: 2019-01-23
title: Hướng dẫn sử dụng tool virsh
categories:
  - Linux
description: Tài liệu hướng dẫn sử dụng tool virsh
author: tuanda
tags: [KVM, Linux, Centos7]
type: Document
---

## Giới thiệu

- `KVM` (Kernel-based virtual machine) là giải pháp ảo hóa cho hệ thống linux trên nền tảng phần cứng x86 có các module mở rộng hỗ trợ ảo hóa (Intel VT-x hoặc AMD-V).
- `Libvirt` là một bộ các phần mềm mà cung cấp các cách thuận tiện để quản lý máy ảo và các chức năng của ảo hóa. Những phần mềm này bao gồm một thư viện API
daemon (`libvirtd`) và các gói tiện tích giao diện dòng lệnh (`virsh`).
- Mục đích chính của `Libvirt` là cung cấp một cách duy nhất để quản lý ảo hóa từ các nhà cung cấp và các loại hypervisor khác nhau. Ví dụ, dòng lệnh `virsh list` có thể được sử dụng để liệt kê ra các máy ảo đang tồn tại cho một số hypervisor được hỗ trợ (KVM, Xen, Vmware ESX, … ). Không cần thiết phải sử dụng một tool xác định cho từng hypervisor.

Cấu trúc cơ bản của `virsh` như sau:
```
virsh [OPTION]... <command> <domain> [ARG]...
```

- Hiển thị thông số cơ bản của node:
```
virsh nodeinfo
```
Kết quả hiển thị:![](/images/img-kvm/virsh_1.png)

- Liệt kê ra tất cả máy ảo (bao gồm cả máy ảo đang stop):
```
virsh list --all
```
Hoặc các máy ảo đang hoạt động:
```
virsh list
```

## Tạo máy ảo



## Các thao tác liên quan đến bật /tắt máy ảo:

| Câu lệnh	| Mô tả |
|-----------|----------|
|help	      | Hiển thị phần trợ giúp|
|start      |	Bật máy ảo đang stop|
|shutdown	  | Gửi lệnh shutdown tới máy ảo|
|destroy    |	Ngay lập tức đóng tất cả ứng dụng và shutdown máy ảo|
|reboot	    | Khởi động lại máy ảo|
|domid	    | Hiển thị ID máy ảo|
|domuuid	  | Hiển thị UUID máy ảo|
|dominfo	  | Hiển thị thông tin máy ảo|
|domstate	  | Hiển thị trạng thái máy ảo |
|save	      | Lưu trạng thái hiện tại của máy ảo vào file|
|restore	  | Restores trạng thái hiện tại của máy ảo từ file.|
|suspend	  | Tạm dừng máy ảo|
|resume	    | Khởi động máy ảo từ trạng thái pause|
|autostart  | Tự động start máy ảo khi hệ thống start|
|autostart --disable | Tắt tự động start máy ảo |
- Stop all máy ảo:

```
$ for i in `sudo virsh list | grep running | awk '{print $2}'` do
    sudo virsh shutdown $i
done
```

## Quản lý tài nguyên VM:

| Câu lệnh	| Mô tả |
|-----------|----------|
|setmem	    | Set RAM cho máy ảo|
|setmaxmem	| Sets maximum memory limit for the hypervisor.|
|setvcpus	  | Thay đổi số vCPU cho máy ảo. Lưu ý không support trên RHEL 5|
|vcpuinfo	  | Hiển thị vCPU của máy ảo|
|vcpupin	  | Controls the virtual CPU affinity of a guest.|
|domblkstat	| Hiển thị thông tin block device của máy ảo|
|domifstat	| Hiển thị thông tin network của máy ảo|
|attach-device	| Attach thiết bị vào máy ảo|
|attach-disk	| Attache ổ đĩa mới vào máy ảo|
|attach-interface	| Attaches network interface mới vào máy ảo|
|detach-device	| Detach thiết bị khỏi máy ảo|
|detach-disk	| Detach ổ đĩa khỏi máy ảo|
|detach-interface	| Detach network interface máy ảo|
|domxml-from-native	| Chuyển đổi cấu hình từ các lib khác sang libvirt XML (libxl ) |
|domxml-to-native	| Chuyển đổi cấu hình libvirt XML sang các định dạng khác |


define	Outputs an XML configuration file for a guest.
undefine	Deletes all files associated with a guest.
migrate	Migrates a guest to another host.
create	Creates a guest from an XML configuration file and starts the new guest.
dumpxml	Outputs the XML configuration file for the guest.


## Quản lý snapshot

- Tạo snapshot:
```
virsh snapshot-create-as --domain vm1 \
--name "snapshot1" \
--description "sau khi update"
```

![](/images/img-kvm/virsh_2.png)

- Liệt kê các snapshot:

```
# virsh snapshot-list vm1
 Name                 Creation Time             State
------------------------------------------------------------
 snapshot1            2019-01-24 11:14:22 +0700 running

root@nhcephbka02:~#
```
- Hiển thị thông tin snapshot:

```
# virsh snapshot-info --domain vm1 --snapshotname snapshot1
Name:           snapshot1
Domain:         vm1
Current:        yes
State:          running
Location:       internal
Parent:         -
Children:       0
Descendants:    0
Metadata:       yes
```

- Quay lại snapshot

```
virsh snapshot-revert --domain vm1  --snapshotname snapshot1  --running
```

- Xóa snapshot

```
# virsh snapshot-delete --domain vm1 --snapshotname snapshot1
Domain snapshot snapshot1 deleted
```

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>