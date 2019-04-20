---
date: 2019-04-16
title: Tích hợp Sentry với Django
categories:
  - Other
description: Hướng dẫn tích hợp Sentry với Django
author: huytm
tags: [Sentry, Linux]
type: Document
---

## Mở đầu

<p align="center">
<img src="/images/img-sentry/cai-dat/img-sentry0.png">
</p>

Ở trong bài viết trước mình đã giới thiệu về sentry và cách [cài đặt](https://blog.cloud365.vn/other/cai-dat-sentry/){:target="_blank"} Sentry trên Linux (Centos 7) theo manual hoặc bằng Docker.

Trong bài viết này mình sẽ hướng dẫn cách tích hợp Sentry với Django để tracking các exception trong project của mình. (Hướng dẫn cài đặt Django tại [đây](https://blog.cloud365.vn/other/gioi-thieu-va-cai-dat-django/){:target="_blank"})

## Xử lý phía Sentry

- Đăng nhập vào Sentry với địa chỉ *http://your_sentry_server:9000*. Ở đây địa chỉ mình sentry server của mình là **10.10.12.193**

<p align="center">
<img src="../images/img-sentry/sentry-django/img-1.png">
</p>

- Nhập thông tin và chọn **Continue**

<p align="center">
<img src="../images/img-sentry/sentry-django/img-2.png">
</p>

- Tạo mới một Project

<p align="center">
<img src="../images/img-sentry/sentry-django/img-3.png">
</p>

- Chọn **Django**, nhập tên Project sau đó chọn **Create Project**

<p align="center">
<img src="../images/img-sentry/sentry-django/img-4.png">
</p>


- Tại màn hình tiếp theo, lưu ý copy lại thông tin dsn (Phần bôi đỏ) sau đó chọn **Got it! Take me to the Issue Stream**

<p align="center">
<img src="../images/img-sentry/sentry-django/img-5.png">
</p>

OK đến đây đã xong phase với Sentry

## Xử lý phía Django 

#### Cài đặt raven

```
pip install raven
```

#### Sửa file **settings.py** 
- Import raven

```
import raven
```

- Bổ sung thêm MIDDLEWARE

```
MIDDLEWARE = [
  ...
  'raven.contrib.django.raven_compat.middleware.Sentry404CatchMiddleware',
]
```

- Bổ sung thêm INSTALLED_APPS 

```
INSTALLED_APPS = [
    'raven.contrib.django.raven_compat',
    'django.contrib.admin',
    ...
]

```

- Bổ sung thêm RAVEN_CONFIG

```
RAVEN_CONFIG = {
    'dsn': 'http://0fa0e0276c764617852c5cf7bb09cabf:e3e595211e384bd986a7448688a84d2d@10.10.12.193:9000/2',
}

```

#### Chạy lại server 

```
python manage.py runserver
```

## Kiểm tra lại 

Thử truy cập vào 1 đường dẫn không tồn tại trên Django 

<p align="center">
<img src="../images/img-sentry/sentry-django/img-6.png">
</p>

Truy cập Sentry và thấy đã có log lỗi

<p align="center">
<img src="../images/img-sentry/sentry-django/img-7.png">
</p>

## Tổng kết

Như vậy, trong bài viết này mình đã tích hợp thành công Sentry với một dự án sử dụng Django của mình để tracking lỗi.

Để thuận tiện hơn trong việc kiểm soát lỗi, ở bài viết sau mình sẽ tích hợp **Sentry** thêm với **Gitlab**.

Chúc các bạn thành công.

>"if you have knowledge let others light their candles in it"

---

### Tài liệu tham khảo

[https://docs.sentry.io/clients/python/integrations/django/](https://docs.sentry.io/clients/python/integrations/django/){:target="_blank"}

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
