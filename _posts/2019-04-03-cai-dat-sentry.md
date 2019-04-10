---
date: 2019-04-04
title: Hướng dẫn cài đặt sentry trên centOS 7
categories:
  - Linux
description: Hướng dẫn cài đặt sentry trên centOS 7
author: huytm
tags: [Python, Linux]
type: Document
---

## Mở đầu

<p align="center">
<img src="/images/img-sentry/img-sentry0.png">
</p>

Bạn đang là Developer và bạn cần tracking các exceptions trong ứng dụng của mình ? Bạn đã bắt log cho ứng dụng của mình nhưng vẫn bị sót các exception? Hoặc đơn giản bạn muốn đếm số lần xuất hiện một lỗi cụ thể ở ứng dụng của bạn.
Có rất nhiều công cụ có thể làm được việc đếm log, thống kê log lỗi ví dụ ELK, Graylog ... Nhưng với môi trường cụ thể là Dev thì các công cụ trên có thể sẽ chưa phù hợp cho lắm.
Và ở bài viết này mình sẽ hướng dẫn các bạn cài đặt và sử dụng **Sentry**, một công cụ tích hợp để quản lý lỗi trong ứng dụng của bạn.

## Sentry là gì ?

[Sentry](https://sentry.io/){:target="_blank"} là một giải pháp *open-source* cung cấp một nền tảng có khả năng *realtime* tracking và logging. Sentry kiểm soát lỗi và hiển thị cho bạn biết khi nào có lỗi, lỗi xảy ra ở đâu, user nào đang bị dính lỗi.
Đặc biệt Sentry hỗ trợ khá nhiều ngôn ngữ và framework trong đó có Ruby, Js, Java, Django, iOS, .NET ...

<p align="center">
<img src="/images/img-sentry/img-sentry1.png">
</p>

## Tại sao mình lại lựa chọn sử dụng Sentry

- Xem log lỗi, các exceptions realtime
- Kiểm soát được user nào đang gặp lỗi
- Kiểm soát được số lần thất bại. Điều này giúp ngăn chặn được những hành vi không hợp pháp đối với ứng dụng của bạn
- Có khả năng tích hợp với các công cụ khác như GitLab, GitHub, Hipchat, Heroku ...
- Hệ thống gửi cảnh báo linh hoạt và nhiều.
- Có báo cáo thống kê theo tuần, tháng ...


## Cài đặt Sentry

Chuẩn bị một [SSD Cloud VPS](https://cloud365.vn){:target="_blank"} CentOS7 có cấu hình tối thiểu như sau: 

<p align="center">
<img src="/images/img-jekyll/image1.png">
</p>

### CÁCH 1: CÀI MANUAL TRÊN CENTOS 7

#### Bước 1. Chuẩn bị

Giả vờ môi trường là lý tưởng, disable SELinux, và stop firewall và cài đặt các gói cần thiết

```
ssh root@you_vps
```

- Tắt firewall, SELinux

```bash
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld
reboot
```

- Sentry yêu cầu python 2.7. Kiểm tra version của python.

```bash
python --version
```

- Cài đặt epel và update

```
yum install epel-release -y
yum update -y
```

- Cài đặt các gói cần thiết

```
yum install python-pip mailcap -y
yum -y install gcc gcc-c++ kernel-devel
yum -y install python-devel libxslt-devel libffi-devel openssl-devel
```

#### Bước 2. Cài đặt Postgresql

- Cài đặt postgres

```
yum install postgresql-server.x86_64 postgresql-contrib -y
```

- Khởi tạo DB

```
postgresql-setup initdb
```

- Start postgres

```
systemctl enable postgresql.service
systemctl start postgresql.service
```

- Sửa lại cấu hình trong file `/var/lib/pgsql/data/pg_hba.conf`

```
# "local" is for Unix domain socket connections only
local all all peer
# IPv4 local connections:
host all all 127.0.0.1/32 md5
# IPv6 local connections:
host all all ::1/128 md5
```

- Khởi động lại dịch vụ

```
systemctl restart postgresql.service
```

#### Bước 3. Cài đặt Redis

- Cài đặt redis

```
yum install redis -y

systemctl enable redis.service
systemctl start redis.service
```

#### Bước 4. Cài đặt virtualenv

```
yum install python-pip
pip install -U virtualenv
```

#### Bước 5. Cài đặt Sentry

> Lưu ý sử dụng python 2.7

- Tạo user sentry

```
useradd sentry
```

- Tạo database cho sentry trên postgres

```
su - postgres
psql template1
create user sentry with password 'cloud365';
alter user sentry with superuser;
create database sentrydb with owner sentry;
\q
exit
```

- Tạo virtualenv

```
su - sentry
virtualenv /home/sentry/sentry_app
```

- Cài đặt sentry

```
source /home/sentry/sentry_app/bin/activate
pip install -U sentry
```

- Khởi tạo sentry

```
/home/sentry/sentry_app/bin/sentry init
```

- Sửa cấu hình database `/home/sentry/.sentry/sentry.conf.py`

```
DATABASES = {
    'default': {
        'ENGINE': 'sentry.db.postgres',
        'NAME': 'sentrydb',
        'USER': 'sentry',
        'PASSWORD': 'thanh123',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

- Khởi tạo cấu hình ban đầu cho sentry

```
SENTRY_CONF=/home/sentry/.sentry sentry upgrade
```

- Tạo user admin tại bước này

```
Would you like to create a user account now? [Y/n]: y
Email: huytm@nhanhoa.com.vn
Password: 
Repeat for confirmation: 
Should this user be a superuser? [y/N]: y
User created: huytm@nhanhoa.com.vn
Added to organization: sentry
```

#### Bước 6. Cài đặt supervisord

- Chuyển về  **root** user

```
su -
```

- Cài đặt supervisord

```
yum install supervisor -y
systemctl start supervisord.service
systemctl enable supervisord.service
```

- Sửa cấu hình ở file `/etc/supervisord.conf`. Và thêm dòng sau vào cuối file

```
echo "files = supervisord.d/*.conf" >> /etc/supervisord.conf
```

- Quản lý tiến trình của sentry bằng việc thiết lập supervisord. Tạo file `/etc/supervisord.d/sentry.conf`

```
[program:sentry-web]
directory=/home/sentry/sentry_app/
environment=SENTRY_CONF="/home/sentry/.sentry"
command=/home/sentry/sentry_app/bin/sentry --config=/home/sentry/.sentry run web
autostart=true
autorestart=true
redirect_stderr=true
user=sentry
stdout_logfile=syslog
stderr_logfile=syslog

[program:sentry-worker]
directory=/home/sentry/sentry_app/
environment=SENTRY_CONF="/home/sentry/.sentry"
command=/home/sentry/sentry_app/bin/sentry --config=/home/sentry/.sentry run worker
autostart=true
autorestart=true
redirect_stderr=true
user=sentry
stdout_logfile=syslog
stderr_logfile=syslog
startsecs=1
startretries=3
stopsignal=TERM
stopwaitsecs=10
stopasgroup=false
killasgroup=true

[program:sentry-cron]
directory=/home/sentry/sentry_app/
environment=SENTRY_CONF="/home/sentry/.sentry"
command=/home/sentry/sentry_app/bin/sentry --config=/home/sentry/.sentry run cron
autostart=true
autorestart=true
redirect_stderr=true
user=sentry
stdout_logfile=syslog
stderr_logfile=syslog
```

- Restart lại supervisord

```
systemctl restart supervisord.service
```

#### Bước 7. Truy cập vào webapp của sentry tại địa chỉ

```
http://<your_vps_ip>:9000
```

<p align="center">
<img src="/images/img-sentry/img-sentry3.png">
</p>

---
### CÁCH 2: CÀI ĐẶT SENTRY BẰNG DOCKER

Login vào vps với tài khoản root:

```
ssh root@your_vps
```

#### Bước 1. Cài đặt git

```
yum install git -y
```

#### Bước 2. Cài đặt Docker

- Cài đặt Docker trên centOS 7 theo bài viết [này](https://blog.cloud365.vn/container/tim-hieu-docker-phan-2/){:target="_blank"} 

#### Bước 3. Cài đặt Docker Compose

- Cài đặt docker-compose

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

#### Bước 4. Build senty container

- Build Sentry với Docker compose

```
git clone https://github.com/getsentry/onpremise.git docker-sentry
cd docker-sentry
docker volume create --name=sentry-data && docker volume create --name=sentry-postgres
cp -n .env.example .env
docker-compose build
```

- Tạo một SENTRY_SECRET_KEY

```
docker-compose run --rm web config generate-secret-key
```

- Add SECRET_KEY vào file .evn

```
# Run `docker-compose run web config generate-secret-key`
# to get the SENTRY_SECRET_KEY value.
SENTRY_SECRET_KEY=SENTRY_SECRET_KEY
```

#### Bước 5. Run Sentry với docker

```
docker-compose run --rm web upgrade
docker-compose up -d
```


#### Bước 6. Truy cập vào webapp của sentry tại địa chỉ

```
http://<your_vps_ip>:9000
```

<p align="center">
<img src="/images/img-sentry/img-sentry4.png">
</p>


## Tổng kết

Như vậy, trong bài viết này mình đã giới thiệu về Sentry và cài đặt Sentry theo 2 cách trên CentOS 7. Trong bài viết tiếp theo mình sẽ hướng dẫn tích hợp Sentry với Framework mình đang sử dụng là **Django**. Cảm ơn các bạn đã theo dõi bài viết, chúc các bạn cài đặt thành công :D.

>"if you have knowledge let others light their candles in it"

---

### Tài liệu tham khảo

[https://docs.sentry.io/server/installation/](https://docs.sentry.io/server/installation/){:target="_blank"}
---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
