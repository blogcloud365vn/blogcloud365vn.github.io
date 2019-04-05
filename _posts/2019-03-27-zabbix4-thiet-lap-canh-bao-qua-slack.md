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







---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>