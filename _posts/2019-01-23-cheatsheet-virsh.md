---
date: 2019-01-23
title: Hướng dẫn sử dụng tool virsh
categories:
  - Linux
description: Tài liệu hướng dẫn sử dụng tool virsh
author: tuanda
tags: [KVM, Linux, virsh]
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

Kết quả hiển thị:

![](/images/img-kvm/virsh_1.png)

- Liệt kê ra tất cả máy ảo (bao gồm cả máy ảo đang stop):

```
virsh list --all
```

Hoặc các máy ảo đang hoạt động:

```
virsh list
```
## Tạo máy ảo

Tạo file XML định nghĩa thông tin máy ảo. Ví dụ:

``` sh
<domain type='kvm'>
  <name>tuan</name>
  <memory unit='MB'>1024</memory>
  <currentMemory unit='MB'>1024</currentMemory>
  <uuid>dc985106-cfd8-11e8-af56-8f2ed6092233</uuid>
  <vcpu>1</vcpu>
  <os>
    <type>hvm</type>
    <boot dev='cdrom'/>
  </os>
  <features>
    <acpi/>
  </features>
  <clock offset='utc'/>
  <on_poweroff>destroy</on_poweroff>
  <on_reboot>restart</on_reboot>
  <on_crash>destroy</on_crash>
  <devices>
    <emulator>/usr/bin/kvm</emulator>
    <disk type="file" device="disk">
      <driver name="qemu" type="raw"/>
      <source file="/var/lib/libvirt/images/tuan.img"/>
      <target dev="vda" bus="virtio"/>
      <address type="pci" domain="0x0000" bus="0x00" slot="0x04" function="0x0"/>
    </disk>
    <disk type="file" device="cdrom">
      <driver name="qemu" type="raw"/>
      <source file="/var/lib/libvirt/images/CentOS-6.9-x86_64-minimal.iso"/>
      <target dev="hdc" bus="ide"/>
      <readonly/>
      <address type="drive" controller="0" bus="1" target="0" unit="0"/>
    </disk>
    <interface type='bridge'>
      <source bridge='br0'/>    
    </interface>
    <controller type="ide" index="0">
      <address type="pci" domain="0x0000" bus="0x00" slot="0x01" function="0x1"/>
    </controller>
    <input type='mouse' bus='ps2'/>
    <graphics type='vnc' port='-1' autoport="yes" listen='0.0.0.0'/>
    <console type='pty'>
      <target port='0'/>
    </console>
  </devices>
</domain>
```

- `name`: Tên của máy ảo.
- `uuid`: uuid của máy ảo
- `maxMemory` : Dung lượng RAM tối đa có thể sử dụng
- `currentMemory` : Dung lượng RAM thực tế đang được sử dụng
- `vcpu placement='static'`: số lượng vCPU của máy

- file domain xml phía trên sẽ tạo ra máy ảo với những thông số sau:
    - 1 GB RAM, 1 vCPU, 1 ổ đĩa
    - Đường dẫn tới ổ đĩa: `/var/lib/libvirt/images/tuan.img`
    - Máy ảo được boot từ CDROM
    - Sử dụng Linux Bridge br0

- Tạo máy ảo từ file XML:

```
virsh define /tmp/new-guest.xml <define nhưng không start>
virsh create /tmp/new-guest.xml <define và start>
virsh start new-guest
```

- Thực hiện việc export thông tin máy ảo ra XML:

```
virsh dumpxml GUEST > /tmp/guest.xml
```

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
|undefine --domain vm1 | Thực hiện việc xóa máy ảo / sau đó thực hiện xóa file ổ đĩa |

- Stop all máy ảo:

```
for i in $(`sudo virsh list | grep running | awk '{print $2}'`) do
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

## Clone máy ảo

```
sudo virt-clone -o web_devel -n database_devel -f /path/to/database_devel.img
```

- `-o` : Máy nguồn.
- `-n` : Máy đích.
- `-f` : Đường dẫn đến file / ổ đĩa máy ảo mới.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>