---
date: 2019-04-02
title: Secure thư mục tmp trên CentOS 7
categories:
  - Linux
description: Secure thư mục tmp trên CentOS 7
author: tuanda
tags: [DNS, Linux]
type: Document
---

Thư mục /tmp được sử dụng để chứa file tạm, và sẽ bị xóa trong một khoảng thời gian nhất định, tùy thuộc vào `tmpwatch` hoặc `systemd-tmpfiles-setup.service`. Tuy nhiên, các file trong thư mục này có quyền thực thi. Vì vậy, bài viết này sẽ hướng dẫn cách để hạn chế các rủi ro khi hacker thực hiện tấn công và thực thi các mã độc trong thư mục /tmp

## Tạo ra 1 file với dung lượng 1GB:
- Đảm bảo đủ dung lượng ổ cứng để thao tác (df -h)
- Sau đó tạo ra 1 file 1GB để sử dụng cho thư mục /tmp

```sh
dd if=/dev/zero of=/dev/tmpDIR bs=1024 count=1000000

1000000+0 records in
1000000+0 records out
1024000000 bytes (1.0 GB) copied, 3.54446 s, 289 MB/s
```

## Format file sang định dạng EXT3:
Sau khi tạo file cần format file định dạng ext3 hoặc định dạng filesystem khác. Khi hiển thị "Proceed anyway?" , bấm "y" để tiếp tục:

```sh
/sbin/mkfs.ext3 /dev/tmpDIR

mke2fs 1.42.9 (28-Dec-2013)
/dev/tmpDIR is not a block special device.
Proceed anyway? (y,n) y
Discarding device blocks: done                            
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
62592 inodes, 250000 blocks
12500 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=260046848
8 block groups
32768 blocks per group, 32768 fragments per group
7824 inodes per group
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done
```

## Backup thư mục /tmp hiện tại:
Backup dữ liệu thư mục /tmp hiện tại (giữ nguyên permisssions) 

```sh
cp -Rpf /tmp /tmpbak
```

## Mount thư mục /tmp mới: 
Sau khi backup thư mục /tmp cũ, thực hiện mount `/dev/tmpDIR` mới tạo vào `/tmp`

```sh
mount -o loop,noexec,nosuid,rw /dev/tmpDIR /tmp
```

Khi mount /tmp với `noexec`, `nosuid` nhằm ngăn việc thực thi file 

Notice we are mounting the `/tmp` directory with noexec and nosuid which will stop direct execution of binary files and not allow set-user-identifier or set-group-identifier bits to take effect respectively.

## Phân quyền /tmp: 

```sh
chmod 1777 /tmp
```

## Copy dữ liệu /tmp cũ: 
Cần copy dữ liệu cũ hiện có ở `/tmpbak` sang thư mục `/tmp` mới:

```sh
cd /tmpbak
cp -Rpf * /tmp/
```

## Sửa fstab: 

Thêm vào dòng cuối cùng của file `/etc/fstab` để thư mục `/tmp` mới mount có thể hoạt động mỗi lần server khởi động lại

```sh
/dev/tmpDIR              /tmp                    ext3    loop,nosuid,noexec,rw 0 0
```

`nodev` – Không cho tạo device 
`nosuid` – Bỏ qua suid bit
`noexec` – Không cho thực thi 

Sau khi hoàn thành thực hiện test thực thi file ở trong thư mục `/tmp`

## Secure thư mục /var/tmp

Backup thư mục `/var/tmp` và tạo symlink từ `/var/tmp` vào `/tmp`

```sh
mv /var/tmp /var/tmpbak

ln -s /tmp /var/tmp
```

Khi thực hiện mv có thể xuất hiện lỗi device đang busy thì cần kiểm tra xem tiến trình nào đang sử dụng thư mục này, và tạm thời stop.

Copy dữ liệu trong /var/tmp vào /tmp

```sh
cp -pR /var/tmpbak/* /tmp/
```

## Script secure tmp

```sh
dd if=/dev/zero of=/dev/tmpDIR bs=1024 count=1000000
/sbin/mkfs.ext3 -F /dev/tmpDIR
cp -Rpf /tmp /tmpbak
mount -o loop,noexec,nosuid,rw /dev/tmpDIR /tmp
chmod 1777 /tmp
cd /tmpbak
cp -Rpf * /tmp/

echo "/dev/tmpDIR /tmp ext3 loop,nosuid,noexec,rw 0 0" >> /etc/fstab

mv /var/tmp /var/tmpbak

ln -s /tmp /var/tmp
cp -pR /var/tmpbak/* /tmp/

```


---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>