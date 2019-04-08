---
title: Cài đặt cấu hình Jenkins trên CentOS7
categories:
  - Linux
description: Cài đặt cấu hình Jenkins trên CentOS7
author: canhdx
tags: [Beginer, Linux, CentOS, Other]
type: Document
---

## Jenkins 

Ở bài viết trước các bạn đã nắm được khái niệm CI là gì?, Jenkins là gì?

Bài viết này sẽ hướng dẫn các bạn cài đặt Jenkins trên CentOS7

<p align="center">
<img src="/images/img-jenkins/jenkins-logo.jpg">
</p>

## Chuẩn bị môi trường để cài đặt Jenkins

1 Server CentOS7 với các yêu cầu như sau
- RAM: >= 1 GB
- Disk: >= 10 GB
- CPU: >= 1 Core
- IP: 10.10.12.89/24
- Yêu cầu có kết nối internet để cài đặt 
- Update và cài đặt openssh-server

Cấu hình firewalld 
```sh
sudo firewall-cmd --permanent --zone=public --add-port=8080/tcp
sudo firewall-cmd --reload
```

## Cài đặt 

Đăng nhập vào Server
```sh 
ssh root@10.10.12.89
```

Vì Jenkins là một ứng dụng Java nên chúng ta cần cài đặt Java để Jenkins có thể họat động 
```sh 
sudo yum install -y java-1.8.0-openjdk-devel
```

Import GPGkey cho Jenkins 
```sh 
curl --silent --location http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo | sudo tee /etc/yum.repos.d/jenkins.repo
```

Kết quả 
```sh 
[jenkins]
name=Jenkins-stable
baseurl=http://pkg.jenkins.io/redhat-stable
gpgcheck=1
```

Bước tiếp theo là add repo của Jenkins 
```sh 
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
```

Tiến hành cài đặt Jenkins
```sh 
sudo yum -y install jenkins
```

Start và enable Jenkins 
```sh 
sudo systemctl start jenkins
sudo systemctl enable jenkins
sudo systemctl status jenkins
```

Kết quả 
```sh 
jenkins.service is not a native service, redirecting to /sbin/chkconfig.
Executing /sbin/chkconfig jenkins on
```

Bây giờ chúng ta có thể truy cập và cấu hình Jenkins 
```sh 
http://your_ip_or_domain:8080
```

## Cấu hình Jenkins trên Web 

Truy cập 
```sh 
http://your_ip_or_domain:8080
```

Màn hình ban đầu sẽ yêu cầu password admin của Jenkins 
<p align="center">
<img src="/images/img-jenkins/jenkins-001.png">
</p>

SSH lại vào Server Jenkins 10.10.12.89 thực hiện tạo Password khởi tạo Admin
```sh 
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Kết quả sẽ xuất hiện dãy 32 ký tự như sau
```sh 
c6eb66cecc234b23b9aee77d27cd7fc7
```

Copy key vừa được tạo paste vào trường Password và click `Continue`
<p align="center">
<img src="/images/img-jenkins/jenkins-002.png">
</p>

Chọn `Install suggested plugins` để cài đặt các Plugin mặc định 
<p align="center">
<img src="/images/img-jenkins/jenkins-003.png">
</p>

Sẽ mất khoảng 1 thời gian để cài đặt các Plugins mặc định
<p align="center">
<img src="/images/img-jenkins/jenkins-004.png">
</p>

Tạo tài khoản Admin và chọn `Save and Continue`
<p align="center">
<img src="/images/img-jenkins/jenkins-005.png">
</p>

Mặc định Jenkins sẽ chạy ở port `8080` chọn `Save and Finish` để tiếp tục 
<p align="center">
<img src="/images/img-jenkins/jenkins-006.png">
</p>

Quá trình cài đặt hoàn tất chọn `Start using Jenkins`
<p align="center">
<img src="/images/img-jenkins/jenkins-007.png">
</p>


Dashboard mặc định sau khi login sẽ như sau
<p align="center">
<img src="/images/img-jenkins/jenkins-008.png">
</p>


## Hoàn tất cài đặt

Bài viết đã hướng dẫn các bạn một cách cơ bản cài đặt Jenkins trên CentOS7. Chi tiết cài đặt các bạn có thể tham khảo tại <a href="https://jenkins.io/doc/" target="_blank">Jenkins Documentation</a>

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>