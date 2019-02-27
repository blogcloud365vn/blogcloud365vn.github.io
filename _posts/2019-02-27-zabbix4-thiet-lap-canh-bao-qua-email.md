---
date: 2019-02-27
title: Zabbix 4.0 - Thiết lập cảnh báo qua email
categories:
  - Monitor
description: Hướng dẫn thiết lập cảnh báo gửi tới người quản trị thông qua email.
author: duydm
tags: [Zabbix]
type: Document
---

Với hệ thống monitor zabbix - server người quản trị hệ thống hoàn toàn có thể thêm rất nhiều các host zabbix-client vào để giám sát các thông số về RAM, Disk, CPU, Network... Ở các bài hướng dẫn trước đã hướng dẫn các bạn cách để triển khai một hệ thống zabbix server và cách thức để giám sát host client. Câu hỏi đặt ra là "Ta phải ngồi trước màn hình monitor 24/24 để quan sát các thông số lên xuống hay sao ?", "Có cách nào để thông báo khi các thông số giám sát có dấu hiệu bất thường ?". Thật đơn giản ở bài hướng dẫn này sẽ hướng dẫn các bạn cách thiết lập cảnh báo qua email, các email sẽ được gửi tới email của bạn khi có điều bất thường xảy ra.

## 1. Cấu hình alert scripts email

### 1.1. Cài đặt môi trường

Do scripts gửi cảnh báo thông qua email được viết bằng ngôn ngữ python nên phải cài môi trường python để chạy được scripts.

```
yum install python2.7
yum install python-pip
```
Kiểm tra

```
python --version
pip --version
```
![](/images/img-zabbix-alert-email/Screenshot_428.png)

### 1.2. Download scripts alert email

Kiểm tra thư mục để các scripts alert cho zabbix server

```
cat /etc/zabbix/zabbix_server.conf | grep alert
```

Kết quả các file scripts được đặt ở thư mục có đường dẫn `/usr/lib/zabbix/alertscripts`

![](/images/img-zabbix-alert-email/Screenshot_429.png)

Vào thưc mục để chứa scripts và download scripts về.

```
cd /usr/lib/zabbix/alertscripts
wget https://raw.githubusercontent.com/domanhduy/zabbix-monitor/master/Alert/Email/zabbix-alert-smtp.sh
chmod +x zabbix-alert-smtp.sh
```

![](/images/img-zabbix-alert-email/Screenshot_430.png)

### 1.3. Chỉnh sửa file scripts

Để người quản trị có thể nhận được các cảnh báo thông qua email, phải thiết lập địa chỉ email gửi đi các cảnh báo trong scripts

```
vi zabbix-alert-smtp.sh
```

Chỉnh sửa 2 tham số sau

```
# Mail Account
MAIL_ACCOUNT = 'emailcuaban@gmail.com'
MAIL_PASSWORD = 'password email cua ban'
```

![](/images/img-zabbix-alert-email/Screenshot_431.png)

## 1.4. Gửi test cảnh báo

Đứng tại thư mục chứa scripts `/usr/lib/zabbix/alertscripts` chạy lệnh test dưới

```
./zabbix-alert-smtp.sh dia_chi_email_nhan_canh_bao@gmail.com "Test" "Alert Zabbix"
```

![](/images/img-zabbix-alert-email/Screenshot_432.png)

Check email nhận cảnh báo

![](/images/img-zabbix-alert-email/Screenshot_433.png)

Như vậy scripts cảnh báo qua email đã chạy thành công.

## 2. Cấu hình cảnh báo email trên Web Zabbix

















---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
