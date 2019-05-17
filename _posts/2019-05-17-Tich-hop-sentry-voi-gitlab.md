---
date: 2019-04-03
title: Tích hợp Sentry với Gitlab
categories:
  - Other
description: Tích hợp Sentry với Gitlab
author: huytm
tags: [Sentry, Gitlab]
type: Document
---

## Mở đầu

Ở trong các bài viết trước của mình, mình đã hướng dẫn cài đặt Sentry và tích hợp Sentry với Django để làm công cụ giám sát log realtime. 

Trong bài viết này mình sẽ tiếp tục hướng dẫn cách tích hợp Sentry với Gitlab, để mỗi khi có một lỗi xảy ra, mình sẽ có thể tạo một Issue và tự động gán Issue đó cho một bạn Dev fix nó

## Yêu cầu

- [Cài đặt Django.](https://blog.cloud365.vn/other/gioi-thieu-va-cai-dat-django/){:target="_blank"}
- [Cài đặt Sentry.](https://blog.cloud365.vn/other/cai-dat-sentry/){:target="_blank"}
- [Tích hợp Sentry và Django.](https://blog.cloud365.vn/other/tich-hop-sentry-django/){:target="_blank"}
- [Gitlab.](https://blog.cloud365.vn/other/huong-cai-dat-gitlab-tren-centos7/){:target="_blank"}

## 1. Xử lý phía Sentry

#### Login vào server sentry với quyền root

```
ssh root@sentry_server
```

#### Cài đặt sentry-plugins

```
su - sentry
source /home/sentry/sentry_app/bin/activate
pip install sentry-plugins
sentry upgrade
sudo systemctl restart supervisord
```


## 2. Xử lý phía Gitlab

Đăng nhập vào Gitlab và tạo một Access Token

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p1.png">
</p>

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p2.png">
</p>

Nhập các thông tin như sau:

- Name : **Gitlab-token-for-sentry**
- Tick chọn : **api**
- Tick chọn : **read_user**
- Tick chọn : **read_repository**
- Chọn : **Create personal access token**

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p3.png">
</p>

Copy và save lại Token này 

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p4.png">
</p>

## 3. Xử lý phía Sentry

Đăng nhập vào Sentry web và chọn vào project của bạn

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p5.png">
</p>

Chọn **Settings**

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p6.png">
</p>

Chọn **All Integrations**

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p7.png">
</p>

Chọn **Enable** Gitlab sau đó chọn **Configure plugin**

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p8.png">
</p>

Nhập các thông tin như sau

- **GitLab URL**: URL của Gitlab
- **Access Token**: Token lấy được từ Bước 2. Xử lý phía Gitlab
- **Repository**: cloud365/myproject - Ở đây chính là đường dẫn đến Repo Project Gitlab của bạn
- **Issue Labels**: Bug (Tùy chọn Tag của bạn trong Issue)
- Chọn **Save Changes**

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p9.png">
</p>

## 4. Kiểm tra

Quay trở lại Project Tracking log trong Sentry và thực hiện các thao tác sau

Chọn Một lỗi bất kỳ

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p10.png">
</p>

Chọn **Gitlab** sau đó chọn **Create New Issue**

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p11.png">
</p>

Nhập các thông tin sau:

- **Title**: Tùy chọn
- **Description**: Tùy chọn (Mình thường để nguyên vì đây chính là log lỗi)
- **Assignee**: Assign cho ai
- **Labels**: Chọn Tag
- Chọn **Create Issue**

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p12.png">
</p>

Quay trở lại Gitlab và chọn Issue trong Project của bạn đã thấy có một Issue được tạo từ Sentry

<p align="center">
<img src="/images/img-sentry/sentry_gitlab/p13.png">
</p>

Như vậy là đã tích hợp thành công

## Tổng kết

Như vậy trong bài viết này mình đã hướng dẫn tích hợp Sentry với Gitlab giúp cho việc quản lý các lỗi trong dự án của bạn được tốt hơn. Ngoài ra các bạn có thể tham khảo thêm bài viết [Tích hợp Gitlab với Slack](https://blog.cloud365.vn/other/tich-hop-gitlab-voi-slack/){:target="_blank"} để quản lý công việc tốt hơn

Chúc các bạn thành công

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
