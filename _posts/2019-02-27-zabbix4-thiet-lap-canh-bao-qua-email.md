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

### Mục lục

[1. Cấu hình alert scripts email](#script)<br>
[2. Cấu hình cảnh báo email trên Web Zabbix](#email)<br>

<a name="script"></a>
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

<a name="email"></a>
## 2. Cấu hình cảnh báo email trên Web Zabbix

### 2.1. Truy cập zabbix server

![](/images/img-zabbix-alert-email/Screenshot_1002.png)


### 2.2. Tạo media type

Media type chính là các kênh kể zabbix server gửi cảnh báo có thể là Email, SMS hay một kênh được tạo ra bằng scripts.

Click `Administrator -> Media types -> Create media type`

![](/images/img-zabbix-alert-email/Screenshot_434.png)

Nhập các thông tin về media type mới

```
Name: Gmail (Tên có thể tùy đặt)
Type: Script
Script name: Tên của script có trong thư mục alert script của server zabbix
Script parameter:
{ALERT.SENDTO}
{ALERT.SUBJECT}
{ALERT.MESSAGE}
```

![](/images/img-zabbix-alert-email/Screenshot_435.png)

Tạo thành công `Media type` mới có tên là `Gmail`

![](/images/img-zabbix-alert-email/Screenshot_436.png)

### 2.3. Set user nhận alert qua email

Click `Administrator -> User -> Lựa chọn user nhận email cảnh báo`

![](/images/img-zabbix-alert-email/Screenshot_437.png)

Click tab `Media` -> `Add` 

![](/images/img-zabbix-alert-email/Screenshot_438.png)

Nhập email nhận cảnh báo

```
Type: Chính là type mà đã tạo ở trên

Sento: Địa chỉ emal sẽ nhận được alert

Use of serverity: Các mức cảnh bảo

Enable: Tích
```

![](/images/img-zabbix-alert-email/Screenshot_440.png)

Như vậy user `Admin` đã được thiết lập nhận cảnh báo qua email.

Click `Update`

![](/images/img-zabbix-alert-email/Screenshot_441.png)

### 2.4. Tạo action

Tạo `action` để khi có sự bất thường đối với các thông số monitor sẽ có alert qua email.

Click `Configuration -> Action -> Create action`

![](/images/img-zabbix-alert-email/Screenshot_442.png)

**Tại tab action**

Nhập thông tin:

```
Name: Tên của action muốn tạo
```

**Tab operation**: Thiết lập thông tin về bản tin cảnh báo gửi đi

![](/images/img-zabbix-alert-email/Screenshot_443.png)

Default subject (Tiêu đề của thông báo)

```
Disaster {HOSTNAME}:{TRIGGER.NAME}-status-{TRIGGER.STATUS}
```

Default message (Nội dung của thông báo)

```
{TRIGGER.NAME} on {HOSTNAME}
Status:{TRIGGER.STATUS}
Severity:{TRIGGER.SEVERITY}
Values:{ITEM.VALUE1}

Item Graphic: [{ITEM.ID1}]
```
![](/images/img-zabbix-alert-email/Screenshot_1023.png)

Set group hoặc chỉ định user nhận được cảnh báo.

![](/images/img-zabbix-alert-email/Screenshot_1024.png)

Set cho action gửi cảnh báo qua kênh nào. Có thể lựa chọn một hoặc nhiều kênh.

![](/images/img-zabbix-alert-email/Screenshot_1025.png)

Click `Add`

![](/images/img-zabbix-alert-email/Screenshot_1028.png)

**Tab Recovery operation**: Thiết lập thông tin về bản tin cảnh báo gửi đi khi không vấn đề được giải quyết, dưới ngưỡng cảnh báo.

Default subject (Tiêu đề của thông báo recovery)

```
OK Resolved: {HOSTNAME}:{TRIGGER.NAME}-status-{TRIGGER.STATUS}
```

Default message (Nội dung của thông báo)

```
{TRIGGER.NAME} on {HOSTNAME}
Status: {TRIGGER.STATUS}
Severity: {TRIGGER.SEVERITY}
Values: {ITEM.VALUE1}

Item Graphic: [{ITEM.ID1}]
```

Sau khi nhập đủ thông tin click `Add`

![](/images/img-zabbix-alert-email/Screenshot_1029.png)

Vậy đã tạo thành công action mới.

![](/images/img-zabbix-alert-email/Screenshot_1030.png)














---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
