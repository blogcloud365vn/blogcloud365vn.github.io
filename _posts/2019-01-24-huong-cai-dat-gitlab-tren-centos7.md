---
title: Hướng dẫn cài đặt Gitlab trên CentOS7
categories:
  - Other
description: Hướng dẫn cài đặt Gitlab trên CentOS7
author: canhdx
tags: [Beginer, Linux, Git, Gitlab, Other]
type: Document
---

## Giới thiệu

Gitlab là một mã nguồn mở của máy chủ Git để quản lý mã nguồn dự án được sử dụng rộng rãi trong các tổ chức doanh nghiệp. 

![](/images/img-gitlab/gitlab-00.jpeg)

Hiện tại Gitlab hỗ trợ 3 phiên bản là 
- CE(Gitlab community editon) Bản opensource
- EE(Gitlab enterprise edition) Bản dành cho doanh nghiệp
- CI(Gitlab continuous intergration) Giải pháp tích hợp liên tục

Bài viết sẽ hướng dẫn chi tiết cách cài đặt phiên bản Gitlab-CE trên Server CentOS7

## Chuẩn bị môi trường cài đặt

- 1 Server CentOS7.5 
- Cấu hình tối thiểu 
    + 1 CPU
    + RAM 2GB
    + Disk 10GB
> Hoặc để phù hợp với yêu cầu chi tiết có thể tham khảo tại trang chủ <a href="https://docs.gitlab.com/ce/install/requirements.html" target="_blank">Gitlab</a>
- Có kết nối Internet 

## Cài đặt các packages cần thiết 
```sh 
yum install epel-release -y 
yum update -y
yum install curl policycoreutils-python openssh-server 
```

## Cấu hình firewalld 
```sh 
sudo firewall-cmd --permanent --zone=public --add-service=http
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --reload
```

## Cấu hình disable Selinux
```sh 
sudo setenforce 0
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=permissive/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
sed -i 's/SELINUX=permissive/SELINUX=disabled/g' /etc/selinux/config
```

## Cài đặt `Postfix` để gửi mail thông báo
```sh 
yum install postfix
systemctl start postfix
systemctl enable postfix
systemctl status postfix
```

## Bổ sung repo cho gitlab-ce
```sh 
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
```

## Cấu hình domain và cài đặt 

Cấu hình domain cho gitlab
```sh 
EXTERNAL_URL="http://gitlab.nhanhoa.local" yum install -y gitlab-ce
```

Chúng ta có thể chỉnh sửa lại cấu hình cài đặt tại `/etc/gitlab/gitlab.rb` sau đó cập nhật cấu hình với câu lệnh sau 
```sh 
gitlab-ctl reconfigure
```

## Truy cập Dashboard và thao tác 

Truy cập trên trình duyệt địa chỉ vừa cài đặt phía trên

- Cấu hình admin/root password cho Gitlab 

	![](/images/img-gitlab/gitlab-01.png)

- Đăng nhập vào Gitlab

	![](/images/img-gitlab/gitlab-02.png)

- Đăng nhập thành công 

	![](/images/img-gitlab/gitlab-03.png)


Để thêm thông tin chi tiết về cấu hình và khai thác có thể tham khảo tại trên trang chủ <a href="https://docs.gitlab.com" target="_blank">Gitlab</a>

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>