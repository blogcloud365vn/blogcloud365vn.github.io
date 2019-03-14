---
date: 2019-03-12
title: Zabbix 4.0 - Thiết lập cảnh báo qua telegram
categories:
  - Monitor
description: Hướng dẫn thiết lập cảnh báo gửi tới người quản trị thông qua telegram.
author: duydm
tags: [Zabbix]
type: Document
---

Zabbix hỗ trợ cảnh báo qua nhiều kênh khác nhau để người quản trị có thể chủ động nhận được thông tin cảnh báo, giúp xử lý kịp thời các sự cố hệ thống của mình. Ngoài email là kênh truyền thống phổ thông nhất hiện nay `telegram` cũng là một ứng dụng được dân kỹ thuật sử dụng, telegram gọn, nhẹ, nhanh, nhạy, có hỗ trợ app trên thiết bị di động. Ở bài hướng dẫn này sẽ hướng dẫn các bạn các bước cài đặt cảnh báo zabbix qua telegram.

### Mục lục

[1. Tạo bot telegram](#script)<br>
[2. Cấu hình alert scripts telegram](#script)<br>
[3. Cấu hình cảnh báo telegram trên Web Zabbix](#email)<br>

<a name="bot"></a>
## 1. Tạo bot telegram

Tạo `bot` hiểu cơ bản là tạo một room được bot API Telegram hỗ trợ lắng nghe và gửi các bản tin cảnh báo. Yêu cầu bạn phải cài đặt ứng dụng telegram và tạo tài khoản để sử dụng.

Truy cập link `https://telegram.me/BotFather`  telegram sẽ yêu cầu bạn mở app telegram và vào channel Botfarther, ở đó có thể tạo bot cho riêng mình.

![](/images/img-zabbix-alert-telegram/Screenshot_1120.png)

![](/images/img-zabbix-alert-telegram/Screenshot_1121.png)

Thực hiện tạo bot

```
/newbot
```

Đặt tên cho bot telegram. Chú ý tên của bot phải là chữ thường và kết thúc bằng chuỗi `bot`

```
namebot
```

Đặt username cho bot

```
namebot
```

Khi tạo bot thành công telegram sẽ trả lại cho bạn thông tin của bot: kênh telegram vừa tạo, chuỗi token.

![](/images/img-zabbix-alert-telegram/Screenshot_1140.png)

Bạn lưu lại chuỗi token để khai báo trong script gửi cảnh báo.

<a name="script"></a>
## 2. Cấu hình alert scripts telegram

### 2.1. Download scripts alert telegram

Vào thưc mục để chứa scripts và download scripts về.

```
cd /usr/lib/zabbix/alertscripts
wget https://raw.githubusercontent.com/domanhduy/zabbix-monitor/master/Alert/TelegramV1/zabbix-telegram.sh
chmod +x zabbix-telegram.sh
```
![](/images/img-zabbix-alert-telegram/Screenshot_1124.png)

### 2.2. Chỉnh sửa file scripts

Để người quản trị có thể nhận được các cảnh báo thông qua telegram, phải sửa tham số `ZBX_URL` là địa chỉ zabbix server, `USERNAME`, `PASSWORD`, `BOT_TOKEN` là chuỗi token telegram bot đã tạo ở trên nhận cảnh cáo.

![](/images/img-zabbix-alert-telegram/Screenshot_1125.png)

<a name="email"></a>
## 3. Cấu hình cảnh báo telegram trên Web Zabbix

### 3.1. Truy cập zabbix server

![](/images/img-zabbix-alert-telegram/Screenshot_1126.png)

### 3.2. Tạo media type

Media type chính là các kênh kể zabbix server gửi cảnh báo có thể là Email, SMS hay một kênh được tạo ra bằng scripts.

Click `Administrator -> Media types -> Create media type`

![](/images/img-zabbix-alert-telegram/Screenshot_1127.png)

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
![](/images/img-zabbix-alert-telegram/Screenshot_1128.png)

Tạo thành công `Media type` mới có tên là `Telegram`

![](/images/img-zabbix-alert-telegram/Screenshot_1129.png)

### 3.3. Set user nhận alert qua Tetegram

Mỗi user muốn nhận cảnh báo zabbix gửi về telegram ta phải lấy được `CHAT ID telegram` của người dùng đó. 

https://api.telegram.org/bot${TOKEN}/getUpdates

Với ${TOKEN} chính là cả chuỗi token API mà khi tạo bot telegram trả về ở trên.

![](/images/img-zabbix-alert-telegram/Screenshot_1130.png)

Chat trên app ở kênh của mình vừa tạo và F5 trình duyệt sẽ thấy thông tin vừa chat, sẽ lấy được `CHAT ID`

![](/images/img-zabbix-alert-telegram/Screenshot_1131.png)

Thiết lập user nhận alert qua telegram

Click `Administrator -> User -> Lựa chọn user nhận cảnh báo`

![](/images/img-zabbix-alert-telegram/Screenshot_1132.png)

Click tab `Media` -> `Add` 

![](/images/img-zabbix-alert-telegram/Screenshot_1133.png)

Thiết lập telegram nhận cảnh báo

```
Type: Chính là type mà đã tạo ở trên
Sento: CHAT ID sẽ nhận được alert
Use of serverity: Các mức cảnh bảo
Enable: Tích chọn
```
![](/images/img-zabbix-alert-telegram/Screenshot_1134.png)

User `Admin` đã được thiết lập nhận cảnh báo qua telegram.

Click `Update`

![](/images/img-zabbix-alert-telegram/Screenshot_1135.png)

### 3.4. Tạo action

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

Set `group` hoặc chỉ định `user` nhận được cảnh báo.

![](/images/img-zabbix-alert-email/Screenshot_1024.png)

Set cho `action` gửi cảnh báo qua kênh nào. Có thể lựa chọn một hoặc nhiều kênh.

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

### 3.5. Test cảnh báo qua telegram

Sử dụng trigger (set up ngưỡng cảnh báo) sẵn có trong template để test gửi cảnh báo.

Click `Configuration -> Lựa chọn host -> Trigger`

![](/images/img-zabbix-alert-telegram/Screenshot_1033.png)

Trigger: `{Host_Centos_10.10.10.119:system.cpu.load[percpu,avg1].avg(5m)}>1` biểu thị giá trị CPU load average trung bình trong 5 phút > 1 sẽ gửi cảnh báo.

Khi thỏa mãn điều kiện của trigger cảnh báo sẽ được tới email.

![](/images/img-zabbix-alert-telegram/Screenshot_1141.png)

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>