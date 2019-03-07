---
date: 2019-03-07
title: Hướng dẫn reset mật khẩu VPS trong KVM bằng guestfish 
categories:
  - Linux
description: Hướng dẫn reset mật khẩu VPS trong KVM bằng guestfish
author: quyenbx
tags: [KVM, Linux, Ubuntu, Centos]
type: Document
---
Guestfish là một công cụ shell và dòng lệnh để kiểm tra và sửa đổi hệ thống tập tin máy ảo. Vì vậy, chúng ta có thể sử dụng công cụ này để chỉnh sửa hệ thống tệp máy ảo, sau đó sửa đổi tệp /etc/shadow để thay đổi mật khẩu cho máy ảo

1. Cài đặt Guestfish 
Môi trường trong bài lab này được thực hiện trên host KVM Centos 7
Chúng ta tiến hành cài đặt Guestfish bằng lênh

```sh
yum install libguestfs-tools
```

2. Tắt Máy ảo
Kiểm tra hostname máy ảo đang chạy trên host KVM bằng lệnh sau

```sh
virsh list
```

![](images\img-change-pass-vps-kvm\1.png)

Dùng lệnh bên dưới để shutdown VM

```sh
virsh shutdown generic
```

Với generic là tên của VM cần reset pass

3. Tìm đường dẫn chứa file Image của máy ảo
Chúng ta sử dụng lệnh bên dưới để tìm đường dẫn chứa image của VM

```sh
virsh dumpxml generic | grep 'source file'
```

![](images\img-change-pass-vps-kvm\2.png)

Với generic là tên của VM.Kết quả hiển thị như ảnh bên dưới

4. Reset password của VM sử dụng guestfish
Tạo một pass mới bằng lênh sau

```sh
openssl passwd -1 QPZWg6YHf8
```

![](images\img-change-pass-vps-kvm\3.png)

Trong lệnh trên thì QPZWg6YHf8 chính là pass root ssh chúng ta muốn dùng để thay đổi cho password root ssh cũ của VM

Kết quả của lệnh trên sẽ trả về một đoạn mã, chúng ta lưu đoạn mã đó lại để đưa vào shadow ở bước tiếp theo

Bắt đầu tiến trình reset password

```sh
guestfish --rw -a /var/lib/libvirt/images/generic.img
```

Chạy lệnh trên với đường dẫn chứa file iamge của VM tìm được ở lênh phía trên.Sau khi chạy lệnh kết quả trả về như ảnh bên dưới

![](images\img-change-pass-vps-kvm\4.png)

Tiếp tục gõ lệnh

```sh
launch
```

![](images\img-change-pass-vps-kvm\5.png)

Tiếp tục sử dụng lệnh

```sh
list-filesystems
```

![](images\img-change-pass-vps-kvm\6.png)

Tiếp theo chúng ta mount phân vùng root với disk tìm thấy ở lệnh trên.Ở ví dụ này là /dev/sda1

```sh
mount /dev/sda1 /
```

![](images\img-change-pass-vps-kvm\7.png)

Tiếp sửa file shadow

```sh
vi /etc/shadow
```

![](images\img-change-pass-vps-kvm\8.png)

Trong file /etc/shadow chú ý dòng 

```sh
root:$6$lXwjyv8mdqrC5FQ2$57w0o6ddJp4OXaeKEWtHQ7mTg.Ai1xnzHnbbg6na93JxFnMmbH4/QEz2L1Y5K9rEA6bXy.RYO7JEwuSPAfEJl0::0:99999:7:::
```

Chúng ta thay thế đoạn mã đã tạo ra ở bước tạo pass mới phía trên vào đoạn 

```sh
$6$lXwjyv8mdqrC5FQ2$57w0o6ddJp4OXaeKEWtHQ7mTg.Ai1xnzHnbbg6na93JxFnMmbH4/QEz2L1Y5K9rEA6bXy.RYO7JEwuSPAfEJl0
```

Nằm giứa 2 dấu hai chấm đầu tiên.Kết quả của dòng này sẽ được thay thế thành

```sh
root:$1$Gty8euaX$7rziCnuUOlu9q2sYR/nKe0::0:99999:7:::
```

Sau khi sửa file trên thì lưu lại

Tiếp tục sử dụng lệnh

```sh
flush
```

```sh
quit
```

Tiếp theo chúng ta start lại VPS và truy cập bằng password QPZWg6YHf8 để kiểm tra

```sh
virsh start generic
```

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>




