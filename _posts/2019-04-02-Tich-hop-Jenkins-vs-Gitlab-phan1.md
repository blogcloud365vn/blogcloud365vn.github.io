---
title: Tích hợp Jenkins với Gitlab phần 1
  - Linux
description: Tích hợp Jenkins với Gitlab phần 1
author: canhdx
tags: [Beginer, Linux, CentOS, Other]
type: Document
---

## Webhook 

Webhook là một interface của Git (Gitlab, GitHub, Bitbuket) cho phép tích hợp các ứng dụng bên ngoài theo dõi bất cứ sự thay đổi nào trên Repo của bạn. Từ push, tạo issue, tạo pull request mới ... Và từ các thông tin này thì ứng dụng được tích hợp đó có thể thực hiện một công việc nào đó 
- Thông báo qua telegram, slack cho bạn là code đã được merge, 
- Có pull request, 
- Comment commit được fix, ... 
Hoặc thậm chí là tự động clone và build mỗi khi có cập nhật sourcecode mới. 

Webhook sẽ chọn các sự kiện (Event) để trả về các payload tương ứng. Nó giúp bạn có thể theo dõi một cách 

## Mô hình kết nối cơ bản 

<p align="center">
<img src="/images/img-jenkins/jenkins-020.png">
</p>

## Chuẩn bị 
Tiến hành create 1 cặp key xác thực để từ Jenkins Server có thể kết nối đến được Gitlab Server
```sh 
canhdx@CanhDX:~$ ssh-keygen -b 2048 -t rsa -f gitlab -q -N ""
canhdx@CanhDX:~$ ls -la gitlab* 
-rw------- 1 canhdx canhdx 1679 Apr  2 14:53 gitlab
-rw-r--r-- 1 canhdx canhdx  395 Apr  2 14:53 gitlab.pub
canhdx@CanhDX:~$
```
> Có thể thực hiện create cặp key xác thực trên bất kỳ PC, Server nào 

- Key `gitlab.pub` sẽ được add vào project trên gitlab
- Key `gitlab` sẽ được add vào Jenkins để Jenkins có thể thực hiện các thao tác trên project được chỉ định của Gitlab


## Cấu hình trên Gitlab 

Đăng nhập vào Gitlab thực hiện tạo mới Project 
<p align="center">
<img src="/images/img-jenkins/jenkins-021.png">
</p>

Bổ sung thông tin project chọn `Create Project`
<p align="center">
<img src="/images/img-jenkins/jenkins-022.png">
</p>

Thực hiện add key 
<p align="center">
<img src="/images/img-jenkins/jenkins-023.png">
</p>

Copy nội dung file `gitlab.pub` đã được tạo 
```sh 
canhdx@CanhDX:~$ cat gitlab.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCfggIs5odPQ2ViCHYPhybenvvxatsFMTG8PnXmG5i1ww2/QpqYqQlIddHYmAPE/ZeBVTisS3SFZTHl2dJVGZ+SnViEpcYqHWxF2qpvchlwuRfDBRqQHR0tw2yQh5lkjh/ZDonngbLfwNUHsZhx1/xiDtz3WGXXwZ/lv+h4Cy97+yVp0l6VneM5OXTMysCNqMrzwjH5s94B/9kVHtCTonwddhTFWjQs/D1l9PYQMqmEsO5HzixhUuFqyFi4n8tpxeFyKQrT8yKNYimPVDF/wqtL1MTag/L74PVoNK806Rvyl18V80AQFxhc8buaLHxl8fFqxZcJWaoWdENy3r2KTY4D canhdx@CanhDX
```

Paste nội dung key vào ô `Key` và click `Add key`
<p align="center">
<img src="/images/img-jenkins/jenkins-024.png">
</p>

Key được add thành công 
<p align="center">
<img src="/images/img-jenkins/jenkins-025.png">
</p>

Truy cập `Access Tokens` để bắt đầu tạo access tokens
<p align="center">
<img src="/images/img-jenkins/jenkins-026.png">
</p>

Ở đây chúng ta bổ sung thêm các thông tin Tên, Ngày hết hạn token, và quyền của token

Token được tạo thành công, lưu trữ token lại tiến hành cấu hình trên Jenkins
<p align="center">
<img src="/images/img-jenkins/jenkins-027.png">
</p>

Thực hiện add commit đầu tiên bằng cách tạo file readme.md 
<p align="center">
<img src="/images/img-jenkins/jenkins-027'1.png">
</p>

<p align="center">
<img src="/images/img-jenkins/jenkins-027'2.png">
</p>

Thành công tạo first commit
<p align="center">
<img src="/images/img-jenkins/jenkins-027'3.png">
</p>

## Thực hiện cấu hình trên Jenkins

Tiến hành cài đặt bổ sung thêm `Gitlab Plugin` trên Jenkins. `Jenkins` --> `Manage Jenkins` --> `Manage Plugins`
<p align="center">
<img src="/images/img-jenkins/jenkins-028.png">
</p>

Chọn tab `Available` filter `Gitlab` và chọn Plugin `Gitlab`
<p align="center">
<img src="/images/img-jenkins/jenkins-029.png">
</p>

Quá trình cài đặt plugin được thực hiện, tích vào Restart Jenkins sau khi Plugin cài đặt
<p align="center">
<img src="/images/img-jenkins/jenkins-030.png">
</p>

Jenkins restart 
<p align="center">
<img src="/images/img-jenkins/jenkins-030'.png">
</p>

Tiến hành SSH vào Jenkins Server để cài đặt thêm git
```sh 
yum install git -y
```

Đăng nhập lại Jenkin với user có quyền admin tiến hành cài đặt các Credentials truy cập `Credentials` -> `global`
<p align="center">
<img src="/images/img-jenkins/jenkins-031.png">
</p>

Copy nội dung file `gitlab` đã được tạo 
```sh 
canhdx@CanhDX:~$ cat gitlab
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAn4ICLOaHT0NlYgh2D4cm3p778WrbBTExvD515huYtcMNv0Ka
mKkJSHXR2JgDxP2XgVU4rEt0hWUx5dnSVRmfkp1YhKXGKh1sRdqqb3IZcLkXwwUa
kB0dLcNskIeZZI4f2Q6J54Gy38DVB7GYcdf8Yg7c91hl18Gf5b/oeAsve/sladJe
lZ3jOTl0zMrAjajK88Ix+bPeAf/ZFR7Qk6J8HXYUxVo0LPw9ZfT2EDKphLDuR84s
YVLhashYuJ/LacXhcikK0/MijWIpj1Qxf8KrS9TE2oPy++D1aDSvNOkb8pdfFfNA
EBcYXPG7mix8ZfHxasWXCVmqFnRDct69ik2OAwIDAQABAoIBAB3C4C0Ihk6fwJbe
BUk4amQ3PcL5sQvy9ditvsnEdGXVkwcQok/Bml3Oveyv0GQ8/LvA7uYW3hLVos3E
Kp5+PWvZt95oH+VTwBP4jLxmiaCiqHBWz3H8PKmBQQxmgJRmgEK7l7d+V6WwEQ9y
b4MuTYTuSQRNlhL/qZnwiKt9SsrS8ejv+fAbHpb/DvYJGyD9zCoAEQthi4HCnBQD
R3v00wL3PHT/f90kPsX+2rzV7hzjjogZEzPpqs9GOQ/6TeXkLJTWz/0P07GGE98b
qTLFEd0nCBkqsEmlKBRanNyIYBZFAe8oPQdxmmdfSNq44IAVoVz+L9cXT2gYKvOh
mVOgKGECgYEAzYj13Kj7jMSXlqWsw0vgDf34DRPaUNuNtA7d9P2ojx4uXgYl8oXV
wGbj7COokCN1bt1tnBVU0amwxzrjtVn5s3MgtRJY/k3kn9FawThcgR3x1f5VHlNE
81ASntGMiYE2f1njdqCHZYHXZBRYu8Raa2o5n/ufKs2K9eSrBp4RvWcCgYEAxqv7
A2NbxTPIA2UrMjjN5+ySug3oexOPP5l8U0T9tz0zy5gDgBx4jrPqaxJLEDFhcfC1
Pd+yDOMnTghXY4To2ftG34PN4OQXLQO9zsXi1/tJkyh/XDr16UZi2uUNbDN4UUkf
MQgVpcWLuWAbXkw2xVcP13S/ze5lGAxj2KUwbQUCgYEAhmDlXj+dN4vREE2Bhq40
7vD9HI++TN8+5lpSA7tUWT4CUrbyWH3oIsMigWFO8+46+SEMaOAJdIrdaWS3O/p3
pzu+RqSYSqvirhrSm0GeOUy6sI54WDtY+TuA8RVQLRat6tV6QjTyUQtzJ5ul6B2Y
gGITCuYYSoPYNqwqpUjzkEECgYBVew3nqUqFEQCQJWlulG5TycBf+Tf8LLBH5xxN
H7QisVylzuRCrP1aOSM1qotNjlGlGrKAyszrKplww7oGAdvGXo1iAm4Ew18NaTyz
yQUPdPl1LcmOv3erRTOnh0I9OfAc2V9oU1gtGVBDFIzyB8zRH+KbC4qzak1pCXu0
bW7mmQKBgQC7iW1ZpNgw65a4SWqlGyUXum0DSIJshZifIGAtw/Pv4KqevddjVd5r
n7o/NHVxTOzY7qN8J3v+3Ba6PdqY/N+JIuQQKo4a2tOOtBEAH/FpZV6YL5E3TVnz
j04qzuNF8drjVnF80VN1lWPA5luS6DBL0Z3+jN3queC+2hhgbDgSpQ==
-----END RSA PRIVATE KEY-----
canhdx@CanhDX:~$ 
```

Chọn `Add Credentials` và điền các thông tin cần thiết
<p align="center">
<img src="/images/img-jenkins/jenkins-032.png">
</p>

Tiến hành add thêm Credentials cho Gitlab API tokens 
<p align="center">
<img src="/images/img-jenkins/jenkins-033.png">
</p>

Bổ sung thêm các thông tin và key API đã tạo trên GitLab 
<p align="center">
<img src="/images/img-jenkins/jenkins-034.png">
</p>

Credentials với API token và SSH key được tạo thành công. Quay ại tran chủ tiếp tục cấu hình 
<p align="center">
<img src="/images/img-jenkins/jenkins-035.png">
</p>

Cấu hình kết nối giữa Jenkins và Gitlab  `Manage Jenkins` -> `Configure System`
<p align="center">
<img src="/images/img-jenkins/jenkins-036.png">
</p>

Cấu hình tên địa chỉ và API tokens của Gitlab và kiểm tra kết nối 
<p align="center">
<img src="/images/img-jenkins/jenkins-037.png">
</p>

Sau khi kiểm tra kết nối thành công thì lưu lại cấu hình cài đặt 
<p align="center">
<img src="/images/img-jenkins/jenkins-038.png">
</p>

## Thực hiện tạo Freestyle Project và test 

Thực hiện tạo mới `Freestyle Project` với name `Jenkins-Gitlab-Webhook` 
<p align="center">
<img src="/images/img-jenkins/jenkins-039.png">
</p>

Cấu hình thêm các thông tin cho Project 

- **General** với thông tin `Gitlab connector`
<p align="center">
<img src="/images/img-jenkins/jenkins-040.png">
</p>

- **Source Code Management** bổ sung các thông tin cần thiết 
	+ Repository URL : `git@10.10.12.94:root/jenkins-webhook.git` <= Tạo trước đó trên Gitlab
	+ Credentials : `Jenkins key` <= Tạo trước đó 
	+ Name : `origin`
	+ Refspec : `+refs/heads/*:refs/remotes/origin/* +refs/merge-requests/*/head:refs/remotes/origin/merge-requests/*`
	+ Branch Specifier : `origin/${gitlabSourceBranch}`
<p align="center">
<img src="/images/img-jenkins/jenkins-041.png">
</p>

- **Build Triggers** 
<p align="center">
<img src="/images/img-jenkins/jenkins-042.png">
</p>

Copy Secret key và `GitLab webhook URL` để cấu hình bên Gitlab
<p align="center">
<img src="/images/img-jenkins/jenkins-042'.png">
</p>

- **Post-build Actions** 
<p align="center">
<img src="/images/img-jenkins/jenkins-043.png">
</p>

- **Build** 
<p align="center">
<img src="/images/img-jenkins/jenkins-044.png">
</p>
Bổ sung shell thực thi và lưu lại Project
<p align="center">
<img src="/images/img-jenkins/jenkins-045.png">
</p>

Lưu lại đường dẫn của Project để cấu hình webhook 
```sh 
http://10.10.12.89:8080/project/Jenkins-Gitlab-Webhook
```

## Quay lại Gitlab tạo Webhook 

Đăng nhập vào Gitlab bằng quyền Admin và truy cập vào `Admin Area` 
<p align="center">
<img src="/images/img-jenkins/jenkins-046'1.png">
</p>

Truy cập `Setting` --> `Network`
<p align="center">
<img src="/images/img-jenkins/jenkins-046'2.png">
</p>

Trong mục `Outbound requests` cho phép các request hook đến local 
<p align="center">
<img src="/images/img-jenkins/jenkins-046'3.png">
</p>

Quay lại Project `Jenkins-Webhook` truy cập `Settings` --> `Integrations` bổ sung các thông tin về URL ...
<p align="center">
<img src="/images/img-jenkins/jenkins-047.png">
</p>

Webhook đã được tạo thành công
<p align="center">
<img src="/images/img-jenkins/jenkins-048.png">
</p>

Kiểm tra kết nối của của Webhook đến Jenkins
<p align="center">
<img src="/images/img-jenkins/jenkins-049.png">
</p>

Kết quả trả về 200 là hook đến Jenkins thành công
<p align="center">
<img src="/images/img-jenkins/jenkins-050.png">
</p>

## Kiểm tra trên Jenkins việc tự động Build Project 
Quay lại project Jenkins kiểm tra quá trình build được tự động thực hiện
<p align="center">
<img src="/images/img-jenkins/jenkins-051.png">
</p>

Kiểm tra chi tiết quá trình build 
<p align="center">
<img src="/images/img-jenkins/jenkins-052.png">
</p>

Truy cập log console để kiểm tra 
<p align="center">
<img src="/images/img-jenkins/jenkins-053.png">
</p>

Log hiển thị build thành công 
<p align="center">
<img src="/images/img-jenkins/jenkins-054.png">
</p>

## Tổng kết
Quá trình Deploy một Project(trong Gitlab) lên Server(Jenkins) được thực hiện tự động mỗi khi có các hành động push trên Repo Jenkins-webhook.

Ở bài kế tiếp chúng ta sẽ tìm cách sử dụng Jenkins để triển khai Repo này lên một Server khác. 
```sh 
############           #############          ###################
#  Gitlab  #    -->    #  Jenkins  #   -->    #  Deploy Server  #
############           #############          ###################
```
---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>