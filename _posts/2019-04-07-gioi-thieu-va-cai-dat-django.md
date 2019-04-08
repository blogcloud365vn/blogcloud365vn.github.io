---
date: 2019-04-08
title: Giới thiệu và cài đặt Django
categories:
  - Other
description: Giới thiệu và cài đặt Django
author: huytm
tags: [Django]
type: Document
---

## Mở đầu

<p align="center">
<img src="/images/img-django/Django-Framework-Logo.png" title="Nguồn: https://codecondo.com/jobs-for-django-developers/django-framework-logo/">
</p>

[Django](https://www.djangoproject.com/){:target="_blank"} là một trong những Web Framework phổ biến nhất được viết bằng Python, cung cấp nhiều tính năng cho việc phát triển web về bảo mật, database access, session, routing, localization ...

Django sử dụng mô hình MVT (Model-View-Template). Về phương thức hoạt động sẽ giống với mô hình MVC, trong đó V (View) sẽ tương đương với C (Controller), T (Template) sẽ tương đương với V (View) ở các framework khác.

Django được sáng lập bởi [Adrian Holovaty](https://en.wikipedia.org/wiki/Adrian_Holovaty){:target="_blank"} và [Simon_Willison](https://en.wikipedia.org/wiki/Simon_Willison){:target="_blank"} vào năm 2003 và publish phiên bản đầu tiên vào năm 2005. Hiện nay, tại thời điểm bài viết này, Django đã release đến phiên bản 2.2.0

Cộng đồng sử dụng và maintaince Django là khá lớn. Nếu bạn vào [Stackoverflow](https://stackoverflow.com/){:target="_blank"} và gõ từ khóa `Django` thì sẽ có khoảng 200.000 kết quả. [Github của Django](https://github.com/django/django){:target="_blank"} có khoảng ~17000 Fork và 26814 commit. Điều đấy chứng tỏ rằng Django là một framwork cũng đáng để sử dụng đấy chứ :D.

Một số website lớn sử dụng Django có thể kể đến như: **Instagram, Mozilla, Disqus, National Geographic, Bitbucker...**. Ngoài ra bạn có thể tìm kiếm các website khác sử dụng Django trong  tại [đây](https://www.djangosites.org/){:target="_blank"}

## Cài đặt Django trên Ubuntu

Vì Django là framework viết bằng Python nên trước hết mình sẽ phải cài đặt python. Mình sẽ sử dụng Python 3 vì đa số các thư viện quan trọng của Python sẽ viết bằng Python 3. Hơn nữa các version mới của Django (2.x) sẽ không còn support Python 2 nữa.

Khi cài Ubuntu, mặc định đã có 2 version Python ở trên. Vì mình sử dụng PYthon 3 nên mình chỉ tập trung kiểm tra python3. Các bạn có thể kiểm tra bằng command sau:

```
python3 --version
```

### <p><span style="color:#007bff">TRƯỜNG HỢP 1: Python < 3.6</span></p>

#### 1. Cài đặt Python và virtualenv

Nếu bạn sử dụng Ubuntu version <= 16.04 thì cần phải add repository. 

> `sudo add-apt-repository ppa:deadsnakes/ppa`

- Cài đặt python 3.6

```
sudo apt-get update
sudo apt-get install python3.6
```

- Cài đặt pip

```
wget https://bootstrap.pypa.io/get-pip.py
sudo python3.6 get-pip.py
```

- Cài đặt virtualenv

```
sudo pip3.6 install virtualenv
```

#### 2. Tạo folder chứa project và tạo mới một virtualenv

```
mkdir my_django
cd my_django
virtualenv env -p python3.6
```

### <p><span style="color:#007bff">TRƯỜNG HỢP 2: Python 3.6</span></p>

#### 1. Cài đặt pip và virtualenv

- Cài đặt pip

```
sudo apt update
sudo apt install python3-pip
```

- Cài đặt virtualenv

```
sudo pip3 install virtualenv
```

#### 2. Tạo folder chứa project và tạo mới một virtualenv

```
mkdir my_django
cd my_django
virtualenv venv
```

---

Sau khi cài đặt Python 3.6 theo một trong hai trường hợp ở trên, mình sẽ tiếp tục thực hiện cài đặt Django

```
cd my_django
source vnv/bin/activate
pip install django
```

Tạo mới project 

```
django-admin startproject my_project
```

Chạy server

```
cd my_project
python manage.py runserver
```

Truy cập vào địa chỉ 

```
http://127.0.0.1:8000
```

<p align="center">
<img src="/images/img-django/django14.png">
</p>


## Cài đặt Django trên Window

#### Bước 1. Cài đặt Python 3.6 

Đầu tiên các bạn hãy truy cập vào địa chỉ [https://www.python.org/downloads/windows/](https://www.python.org/downloads/windows/){:target="_blank"} và chọn phiên bản Python 3.6.6 để download.

<p align="center">
<img src="/images/img-django/django1.png">
</p>

Sau khi download xong, mình tiến hành cài đặt Python bằng quyền Administrator

<p align="center">
<img src="/images/img-django/django2.png">
</p>

Chọn **Add Python 3.6 to PATH** → **Install Now**

<p align="center">
<img src="/images/img-django/django4.png">
</p>

Đóng lại cửa sổ cài đặt sau khi cài đặt thành công

<p align="center">
<img src="/images/img-django/django5.png">
</p>

Kiểm tra lại version của Python

<p align="center">
<img src="/images/img-django/django6.png">
</p>

```
python --version
```

<p align="center">
<img src="/images/img-django/django7.png">
</p>



Cũng tại cửa sổ **Command Prompt** này, mình sẽ cài đặt virtualenv

```
pip install virtualenv
```

<p align="center">
<img src="/images/img-django/django8.png">
</p>

Tạo mới một folder chứa project

```
mkdir my_django
cd my_django 
```

<p align="center">
<img src="/images/img-django/django9.png">
</p>

Tạo một virtual environment mới

```
virtualenv env
env\Scripts\activate 
```

<p align="center">
<img src="/images/img-django/django10.png">
</p>

Cài đặt Django

```
pip install django 
```

<p align="center">
<img src="/images/img-django/django11.png">
</p>

Tạo mới một project django 

```
django-admin startproject myproject
```

<p align="center">
<img src="/images/img-django/django12.png">
</p>

Run server 

```
cd myproject
python manager.py runserver
```

<p align="center">
<img src="/images/img-django/django13.png">
</p>

Sử dụng Browser và truy cập vào địa chỉ

```
http://127.0.0.1:8000
```

<p align="center">
<img src="/images/img-django/django14.png">
</p>

Như vậy là đã cài đặt thành công 


## Tổng kết

Như vậy, trong bài viết này mình đã giới thiệu về Django, một framework được viết bằng Python, sử dụng để phát triển website. Đồng thời trong bài viết mình cũng đã hướng dẫn cài đặt Django và khởi tạo một project django đầu tiên trên cả Ubuntu và Windows. 

Tóm lại để  có được một project django mình sẽ làm theo các bước sau:

- Cài đặt Python 3.6.
- Cài đặt pip (Python Package Index - một hệ thống package-management cho Python).
- Cài đặt virtualenv sử dụng pip.
- Cài đặt Django.
- Tạo mới một project Django.
- Run server

Chúc các bạn thành công

>"if you have knowledge let others light their candles in it"

---

### Tài liệu tham khảo

[https://docs.sentry.io/server/installation/](https://docs.sentry.io/server/installation/){:target="_blank"}
---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
