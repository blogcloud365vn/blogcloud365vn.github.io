---
title: Tạo Jenkins Project(Job) đơn giản
categories:
  - Linux
description: Tạo Jenkins Project(Job) đơn giản
author: canhdx
tags: [Beginer, Linux, CentOS, Other]
type: Document
---

## Jenkins Project(Job) là gì 

Trong Jenkins thì khái niệm `Project` và `Job` là tương đương. Thời điểm hiện tại thì khái niệm `Job` dần dần bị loại bỏ https://jenkins.io/doc/book/glossary/

Project(Job): Là cách admin khai báo một công việc mà jenkins cần thực hiện như là: Tạo môi trường, Clone sourcecode, Cài đặt, Cấu hình, gửi mail thông báo. Các công việc này có thể được hỗ trợ bởi các Plugin hoặc sử dụng `Freestyle Project`
 để tùy biến cách thức chạy, như là BashShell hoặc PowerShell

Build: Hành động thực thi Project

Freestyle Project: Là tùy chọn linh họa cho việc khởi tạo các Project. Bao gồm `Tạo item` --> `Build` --> `Console Output`

## Thao tác tạo Free Style Project cơ bản 

Login bằng tài khoản Admin 

Tạo mới Item bằng các click `New item` nằm ở panel trái màn hình 
<p align="center">
<img src="/images/img-jenkins/jenkins-009.png">
</p>

Nhập tên item và chọn `Free Style Project` và chọn `OK`
<p align="center">
<img src="/images/img-jenkins/jenkins-010.png">
</p>

Tại đây có nhiều sections để cấu hình 1 **Project Jenkins**
<p align="center">
<img src="/images/img-jenkins/jenkins-011.png">
</p>

- General 
- Source Code Management 
- Build Triggers
- Build Environment
- Build 
- Post-build Actions

Ở phần `General` miêu tả cụ thể về Project 
<p align="center">
<img src="/images/img-jenkins/jenkins-012.png">
</p>


Ở phần `Source Code Management` ở đây chúng ta chọn `None` vì không sử dụng Git hay SVM gì cả
<p align="center">
<img src="/images/img-jenkins/jenkins-013.png">
</p>


Bỏ qua `Build Triggers` và `Build Environment` 


Ở phần Build chúng ta chọn `Add build step` chọn `Execute Shell` cho nội dung kịch bản shell 
<p align="center">
<img src="/images/img-jenkins/jenkins-014.png">
</p>

Nội dung thực hiện echo ra màn hình và đẩy log console 

Chọn `Save` để lưu project lại 

Tiến hành build Project 
<p align="center">
<img src="/images/img-jenkins/jenkins-015.png">
</p>

Kết quả 
<p align="center">
<img src="/images/img-jenkins/jenkins-016.png">
</p>

Phần Console log sẽ hiển thị chi tiết quá trình Build
<p align="center">
<img src="/images/img-jenkins/jenkins-017.png">
</p>

<p align="center">
<img src="/images/img-jenkins/jenkins-018.png">
</p>

Quay lại màn hình quản 
<p align="center">
<img src="/images/img-jenkins/jenkins-019.png">
</p>


## Tổng kết

Chúng ta đã thực hiện tạo 1 Project cơ bản trên Jenkins, thực tế CI/CD đòi hỏi nhiều project kết hợp lại với nhau để hoạt động


---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>