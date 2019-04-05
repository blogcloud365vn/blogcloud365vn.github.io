---
date: 2019-03-27
title: Zabbix 4.0 - Thiết lập cảnh báo qua slack
categories:
  - Monitor
description: Hướng dẫn thiết lập cảnh báo gửi tới người quản trị thông qua slack.
author: duydm
tags: [Zabbix]
type: Document
---

Ở các bài viết trước đã hướng dẫn cho các bạn cài đặt để nhận cảnh báo từ zabbix qua <a href="https://blog.cloud365.vn/monitor/zabbix4-thiet-lap-canh-bao-qua-email/" target="_blank">email</a>, <a href="https://blog.cloud365.vn/monitor/zabbix4-thiet-lap-canh-bao-qua-telegram/" target="_blank">telegram</a>. Ở bài viết này hướng dẫn cho các bạn thiết lập một cảnh báo qua Slack một channel chat được rất nhiều công ty, tổ chức áp dụng trong nội bộ của mình.

![](/images/img-zabbix-slack/Screenshot_1270.png)

### Mục lục

[1. Tạo channel slack](#channel)<br>
[2. Cấu hình alert scripts slack](#script)<br>
[3. Cấu hình cảnh báo slack trên Web Zabbix](#telegram)<br>

<a name="channel"></a>
## 1. Tạo channel slack

Đăng nhập vào workspace của bạn

![](/images/img-zabbix-slack/Screenshot_1271.png)

Nhập địa chỉ `email/password`

![](/images/img-zabbix-slack/Screenshot_1272.png)

Để có thể nhận được cảnh báo qua kênh slack bạn phải tạo một channel. Tạo channel <a href="https://my.slack.com/services/new/incoming-webhook" target="_blank">tại đây</a> sau khi đã đăng nhập thành công Slack trên trình duyệt web.

![](/images/img-zabbix-slack/Screenshot_1273.png)

Tạo channel mới

![](/images/img-zabbix-slack/Screenshot_1274.png)

Nhập thông tin channel mới

![](/images/img-zabbix-slack/Screenshot_1275.png)

Lấy địa chỉ webhook: Lựa chọn channel vừa tạo -> click `Add Incomming WebHooks integration`

![](/images/img-zabbix-slack/Screenshot_1276.png)

Địa chỉ webhook dùng để cấu hình cho phần sau.

![](/images/img-zabbix-slack/Screenshot_1277.png)

<a name="script"></a>
## 2. Cấu hình alert scripts slack

### 2.1. Download scripts alert slack

Vào thưc mục để chứa scripts và download scripts về.

```
cd /usr/lib/zabbix/alertscripts
wget https://raw.githubusercontent.com/domanhduy/zabbix-monitor/master/Alert/Slack/slack.sh
chmod +x slack.sh
```

![](/images/img-zabbix-slack/Screenshot_1278.png)

### 2.2. Chỉnh sửa file scripts

Chỉnh sửa địa chỉ webhook  muốn nhận cảnh báo trong script

Đứng trong thư mục `/usr/lib/zabbix/alertscripts` chỉnh sửa file `slack.sh`

![](/images/img-zabbix-slack/Screenshot_1279.png)

Test xem script đã hoạt động hay chưa.

Đứng trong thư mục `/usr/lib/zabbix/alertscripts` chạy câu lệnh `bash slack.sh`

![](/images/img-zabbix-slack/Screenshot_1280.png)

## 3. Cấu hình cảnh báo slacktrên Web Zabbix

### 3.1. Truy cập zabbix server

![](/images/img-zabbix-slack/Screenshot_1281.png)

### 3.2. Tạo media type

Click `Administrator -> Media types -> Create media type`

![](/images/img-zabbix-slack/Screenshot_1282.png)

Nhập các thông tin về `media type` mới

```
Name: Telegram (Tên có thể tùy đặt)
Type: Script
Script name: Tên của script có trong thư mục alert script của server zabbix
Script parameter:
{ALERT.SENDTO}
{ALERT.SUBJECT}
{ALERT.MESSAGE}
```
![](/images/img-zabbix-slack/Screenshot_1283.png)

Tạo thành công `Media type` mới có tên là `slack`

![](/images/img-zabbix-slack/Screenshot_1284.png)

### 3.3. Set user nhận alert qua Slack

Thiết lập user nhận alert qua slack

Click `Administrator -> User -> Lựa chọn user nhận cảnh báo`

![](/images/img-zabbix-slack/Screenshot_1285.png)

Click tab `Media` -> `Add` 

![](/images/img-zabbix-slack/Screenshot_1286.png)

Thiết lập slack nhận cảnh báo

```
Type: Chính là type mà đã tạo ở trên
Sento: channel sẽ nhận được alert
Use of serverity: Các mức cảnh bảo
Enable: Tích chọn
```
![](/images/img-zabbix-slack/Screenshot_1287.png)

User `Admin` đã được thiết lập nhận cảnh báo qua telegram.

Click `Update`

![](/images/img-zabbix-slack/Screenshot_1288.png)

### 3.4. Tạo action

Tạo `action` để khi có sự bất thường đối với các thông số monitor sẽ có alert qua telegram.

Click `Configuration -> Action -> Create action`

![](/images/img-zabbix-slack/Screenshot_442.png)

**Tại tab action**

Nhập thông tin:

```
Name: Tên của action muốn tạo
```

**Tab operation**: Thiết lập thông tin về bản tin cảnh báo gửi đi

![](/images/img-zabbix-slack/Screenshot_443.png)

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
![](/images/img-zabbix-slack/Screenshot_1023.png)

Set `group` hoặc chỉ định `user` nhận được cảnh báo.

![](/images/img-zabbix-slack/Screenshot_1024.png)

Set cho `action` gửi cảnh báo qua kênh nào. Có thể lựa chọn một hoặc nhiều kênh.

![](/images/img-zabbix-slackScreenshot_1142.png)

Click `Add`

![](/images/img-zabbix-slack/Screenshot_1028.png)

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

![](/images/img-zabbix-slack/Screenshot_1029.png)

Vậy đã tạo thành công action mới.

![](/images/img-zabbix-slack/Screenshot_1030.png)

### 3.5. Test cảnh báo qua telegram

Sử dụng trigger (set up ngưỡng cảnh báo) sẵn có trong template để test gửi cảnh báo.

Click `Configuration -> Lựa chọn host -> Trigger`

![](/images/img-zabbix-slack/Screenshot_1033.png)

Trigger: `{Host_Centos_10.10.10.119:system.cpu.load[percpu,avg1].avg(5m)}>1` biểu thị giá trị CPU load average trung bình trong 5 phút > 1 sẽ gửi cảnh báo.

Khi thỏa mãn điều kiện của trigger cảnh báo sẽ được tới channel slack.

![](/images/img-zabbix-slack/Screenshot_1141.png)

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>