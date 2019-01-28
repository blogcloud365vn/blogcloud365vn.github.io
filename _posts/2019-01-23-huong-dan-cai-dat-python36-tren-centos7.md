---
title: Python cho Sysadmin - Hướng dẫn cài đặt Python3.6 trên CentOS7
categories:
  - Linux
  - Other
description: Hướng dẫn cài đặt Python3.6 trên CentOS7
author: canhdx
tags: [Beginer, Linux, Other]
type: Document
---

# 1. Tại sao lại sử dụng Python và ... sao lại là Python3

Python là một ngôn ngữ lập trình bậc cao không còn quá xa lạ với mọi người. Với quan điểm là một SystemAdmin thì việc sử dụng một ngôn ngữ nào đó hỗ trợ tốt nhất cho việc thao tác với hệ thống là điều khá quan trọng. 

Theo các số liệu thực tế tổng hợp của [github.blog](https://github.blog/2018-11-15-state-of-the-octoverse-top-programming-languages/) thì Python là một ngôn ngữ đáng để tìm hiểu.

Theo đánh giá bản thân 
- Cấu trúc câu lệnh dễ tiếp cận cho người mới
- Nhiều module hỗ trợ 
- ...

![](/images/img-python/python2-vs-python3.jpg)

Đa số các bản python cài đặt sẵn trong OS linux như CentOS, Ubuntu nhằm mục đích ổn định nên thường sử dụng bản mặc định là 2.7.x và hơn hết là chậm trong việc update các tính năng mới.

Hiện tại phiên bản mới nhất của Python là 3.7.x. Các ứng dụng hiện tại có xu hướng chuyển lên python version mới hơn để phát triển nên việc đi tìm hiểu 1 bản python cũ hơn thì mình sẽ không làm vậy, cấu trúc của Python3x so với Python2x thay đổi không nhiều nên cứ Python3x mà khai thác. 

Bài viết sẽ hướng dẫn các bạn cài đặt nhanh môi trường Python3.6 trên CentOS7

# 2. Cài đặt 
Chúng ta có thể lựa chọn cài đặt Python3.6 từ Repo hoặc build từ Sources

## 2.1 Cài đặt từ Repo

Thêm Repo 
```sh 
sudo yum install -y https://centos7.iuscommunity.org/ius-release.rpm
```

Update để cập nhật repo mới 
```sh 
sudo yum update
```

Download và cài đặt python36
```sh 
sudo yum install -y python36u python36u-libs python36u-devel python36u-pip
```

Sau khi cài đặt chúng ta có thể kiểm tra 
```sh 
python3.6 -V
```

Gỡ cài đặt 
```sh 
sudo yum remove -y python36u python36u-libs python36u-devel python36u-pip
```

## 2.2 Cài đặt từ sourcecode

Cài đặt bộ công cụ cần thiết
```sh 
sudo yum groupinstall -y "Development Tools"
```

Download Python source 
```sh 
wget https://www.python.org/ftp/python/3.6.4/Python-3.6.4.tar.xz
```

Giải nén và cài đặt 
```sh
tar -xJf Python-3.6.4.tar.xz
cd Python-3.6.4
```

Chạy script cấu hình
```
./configure
```

Tiến hành compile cài đặt 
```sh 
make 
make altinstall
```
> `altinstall` sẽ tránh việc ghi đè thay thế python mặc định của hệ thống đang chạy `/usr/bin/python` mà sẽ thành 1 binary chương trình riêng biệt 

Gỡ cài đặt 
```sh 
cd Python-3.6.4
make clean
make uninstall
make -n install
```

## 2.2 Cài đặt Virtual Environment (venv)

Trong quá trình sử dụng python3 để chạy các script, ứng dụng. Việc cài đặt thêm các requirement là điều không thể tránh khỏi. Nhằm tạo một môi trường tách biệt để chạy các script, chương trình này tránh gây xung đột với các package đang có trên hệ thống, chúng ta sử dụng `venv` để tạo một Virtual Environment. Các lib, module sẽ được cài đặt mới trong môi trường ảo này mà không ảnh hưởng đến hệ thống.

Cài đặt pip
```sh 
wget https://bootstrap.pypa.io/get-pip.py
sudo python3.6 get-pip.py
```

Cài đặt venv
```sh 
sudo pip3.6 install virtualenv
```

Tạo môi trường riêng cho ứng dụng 
```sh 
mkdir Project
cd Project
virtualenv venv -p python3.6
source venv/bin/activate
```

Kiểm tra python version 
```sh 
python -V
```

Deactive không sử dụng venv nữa
```sh
deactivate
```

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
