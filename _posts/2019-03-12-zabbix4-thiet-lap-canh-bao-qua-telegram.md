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
![](/images/img-zabbix-alert-telegram/Screenshot_1122.png)

Đặt tên cho bot telegram. Chú ý tên của bot phải là chữ thường và kết thúc bằng chuỗi `bot`

```
namebot
```

Đặt username cho bot

```
namebot
```

Khi tạo bot thành công telegram sẽ trả lại cho bạn thông tin của bot: kênh telegram vừa tạo, chuỗi token.

![](/images/img-zabbix-alert-telegram/Screenshot_1123.png)

Bạn lưu lại chuôiz token để khai báo trong script gửi cảnh báo.

<a name="script"></a>
## 2. Cấu hình alert scripts telegram

Vào thưc mục để chứa scripts và download scripts về.

```
cd /usr/lib/zabbix/alertscripts
wget https://raw.githubusercontent.com/domanhduy/zabbix-monitor/master/Alert/TelegramV1/zabbix-telegram.sh
chmod +x zabbix-telegram.sh
```
![](/images/img-zabbix-alert-telegram/Screenshot_1124.png)
























---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>