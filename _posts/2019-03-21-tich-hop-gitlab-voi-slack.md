---
title: "Tích hợp Gitlab với Slack"
categories:
  - Others
description: Tích hợp Gitlab với Slack
author: huytm
tags: [Git]
type: Document
---

## Mở đầu

Bạn thường xuyên làm việc với Git? Nhưng lại [cài đặt và sử dụng Gitlab CE](https://blog.cloud365.vn/other/huong-cai-dat-gitlab-tren-centos7/){:target="_blank"} và chỉ chạy Private mà không có public gì ra ngoài. Mỗi khi rời xa máy công ty là bạn phải VPN lên công ty để truy cập, và xem tình hình công việc ngày hôm nay đến đâu, có những *Issue* nào tạo mới, phần code của bạn đã được merge hay chưa ?...

Nếu bạn sử dụng máy tính cá nhân thì không nói, nhưng nếu bạn có công chuyện không thể sử dụng máy tính một vài ngày thì sao có thể track được xem công việc của nhóm tới đâu.

Giải pháp mình đưa ra là tích hợp Gitlab với Slack để làm một kênh nhận thông báo các sự kiện quan trọng đối với source code của bạn.

## Tại sao lại chọn Slack ?

Vì Gitlab CE đã có sẵn một phần có thể tích hợp với Slack. Bản thân Slack cũng là phần mềm phổ biến được nhiều doanh nghiệp ưa dùng bởi tính bảo mật của nó.

## Tích hợp Gitlab với Slack

Bản chất của việc nhận gửi nhận thông báo này là Slack sẽ tạo một **Webhooks** và mỗi sự kiện của Gitlab sẽ được gửi tới **Webhooks**. Bằng các dòng code bên trong, sau khi kiểm tra đúng sự kiện, Webhook này sẽ hiển thị thông báo lên channel chat mà bạn cấu hình.

### 1. Tạo channel Slack để nhận thông báo.

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh1.png">
</p>


Ở đây mình đặt tên channel là **github-notify**. Các bạn đặt tên gì cũng được.

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh2.png">
</p>

Nhập thêm một số thông tin cần thiết sau đó chọn **Create Channel**

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh3.png">
</p>


### 2. Tạo Slack Webhooks

Đầu tiên bạn phải truy cập vào [https://api.slack.com/apps](https://api.slack.com/apps){:target="_blank"} Sau đó chọn **Create New App**

Một màn hình hiện ra, các bạn nhập thông tin vào như sau

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh4.png">
</p>

Chọn Incoming Webhooks

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh5.png">
</p>

Chọn **On** tại phần Active Incoming Webhooks và chọn **Add New Webhooks to Workspace**

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh6.png">
</p>

Chọn channel vừa khởi tạo tại Bước 1.

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh7.png">
</p>

Chọn **Authorize**

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh8.png">
</p>

Copy lại **Webhooks URL**

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh9.png">
</p>

### 3. Setting Gitlab đây thông báo tới Slack

Chọn project muốn tracking, sau đó chọn **Settings** → **Integrations** 

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh10.png">
</p>

Chọn **Slack notifications**

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh11.png">
</p>

Chọn **Active**, nhập Webhooks URL copy từ bước trước vào ô **Webhooks**. Sau đó chọn **Test settings and save changes** 

<p align="center">
<img src="/images/img-gitlab-slack/Anh12.png">
</p>

Nếu nhận được một thông báo được gửi đến channel ở Slack thì tức là Setting đã thành công.

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh13.png">
</p>

### 4. Tạo một Issue để kiểm tra lại

Vẫn trong Project đang setting, chọn **Issue**

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh14.png">
</p>

Tạo một Issue để test 

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh15.png">
</p>

Kiểm tra channel Slack

<p align="center">
<img width="600" height="400" src="/images/img-gitlab-slack/Anh16.png">
</p>

--
## Tổng kết

Như vậy trong bài viết này mình đã giới thiệu các bạn cách tích hợp Gitlab với Slack. Hy vọng với bài viết này các bạn có thể vận dụng để quản lý được công việc của mình tốt hơn. Cảm ơn các bạn đã theo dõi và chúc các bạn áp dụng thành công :D

>"if you have knowledge let others light their candles in it"

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>

