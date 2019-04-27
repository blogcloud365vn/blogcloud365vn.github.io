---
date: 2019-04-11
title: Zabbix 4.0 - Backup câú hình
categories:
  - Monitor
description: Hướng dẫn backup cấu hình zabbix server
author: niemdt
tags: [Zabbix, backup]
type: Document
---

## Tổng quan

Zabbix là hệ thống giám sát các thiết bị mạng mã nguồn mở, dễ triển khai và dễ sử dụng. Trong khi sử dụng đôi khi bạn gặp phải một số vấn đề không mong muốn đôi khi là không thể khôi phục. Để tránh việc phải cấu hình lại từ đầu zabbix server bằng cách thông thường là cài đặt sau đó vào để add lại từng host. Trong bài viết này tôi sẽ hướng dẫn bạn cách backup lại các cấu hình của zabbix server. Ở đây tôi chỉ tiến hành backup cấu hình mà không backup dữ liệu từ các host gửi về trong khoảng thời gian trước đó nên việc backup sẽ không tốn nhiều tài nguyên. 

## Mô hình

Backup ở đây thực chất là backup lại DB của hệ thống. Chính vì vậy bạn có thể tiến hành backup trực tiếp trên zabbix server hoặc bạn có thể đứng ở trên một máy khác để backup. Với cách thứ hai sẽ được ưu tiên dùng hơn vì bạn có thể dễ dàng quản lý các file backup tránh trường hợp khi zabbix server có vấn đề bạn ko thể vào để lấy các file đã backup ra để tiến hành restore. Trong bài lab này tôi sẽ sử dụng cách thứ hai để backup.

![](/images/img-backup-zabbix/1.png)

Ở đây tôi đứng ở trên host có IP 10.10.10.162 để tiến hành backup zabbix server

## Thực hiện

Với mô hình như bên trên tôi chỉ cân thực hiện trên host mà không cần thao tác trên zabbix server

Thực hiện lần lượt các lệnh sau

```
wget https://raw.githubusercontent.com/niemdinhtrong/ghichep-zabbix/master/scripts/zabbix-backup.setup

chmod +x zabbix-backup.setup

./zabbix-backup.setup
```

Tiếp theo điền thông tin của zabbix server bằng cách sửa file `zabbix-backup.sh`. 

```
vi zabbix-backup.sh
```

Sửa thông tin ở những dòng sau

```
USER_NAME="zabbix"
DBHOST="IP-zabbix-server"
DBNAME="zabbix"
DBPASSWORD="password"
DBPORT="3306"
```

Trong đó:

 * `USER_NAME`: tên của user trong MySQL có quyền với DB của zabbix
 * `DBHOST`: địa chỉ của zabbix server
 * `DBNAME`: tên DB dùng cho zabbix server
 * `DBPASSWORD`: password để truy cập zabbix server
 * `DBHOST`: port để truy cập mysql
Sau khi sửa xong tiến hành lưu lại file.

Lưu ý: 
 * Để tiến hành backup từ xa thì bạn user có quyền với DB trong MySQL cần phải có quyền truy cập từ xa. 
 * Nếu bạn cài zabbix server và cài MySQL trên hai host khác nhau thì địa chỉ bên trên bạn cần chỉ ra máy đến chứa DB.

Để tiến hành backup bạn thực thi scripts `zabbix-backup.sh`

```
./zabbix-backup.sh
```

Đợi cho câu lệnh chạy xong. Nó có thể nhanh hoặc chậm tùy thuộc vào dung lượng của DB.

Sau khi lệnh này chạy xong bạn có thể thấy file backup sẽ được lưu trong thư mục `/root/zabbix/backup-file`. Các file backup sẽ có định dạng `zabbix-năm-tháng-ngày-giờ-phút-giây.sql` như sau

![](/images/img-backup-zabbix/2.png)

Các file backup mặc định sẽ được lưu trong 5 ngày gần nhất. Để thay đổi số ngày bạn có thể thay đổi dòng sau

```
SAVE_FILE="5"
```

**Restore**

Khi cần restore lại một bản backup thì bạn thực hiện lệnh sau:

```
mysql -u username -p DBname < name-file-backup.sql
```

Thực hiện lệnh này trên zabbix server. `name-file-backup.sql` là đường dẫn đến file backup. 

Nếu trong thường hợp zabbix server không thể khôi phục bạn cần cài đặt lại zabbix server. Trong trường hợp này bạn vẫn cài đặt lại zabbix server trên một server khác một cách bình thường sau đó bạn tiến hành import lại DB của một file backup vào lại. Như vậy mọi cấu hình trên zabbix server sẽ không bị mất mà chỉ bị mất đi các metric ở thời điểm trước đó.

Lưu ý: Nếu phải cài lại zabbix server trên một server khác thì cần cấu hình lại địa chỉ IP là địa chỉ IP của server cũ để các host có thể gửi metric đến.

## Tổng kết

Như vậy với cách này bạn sẽ không phải lo việc khi gặp vấn đề trên zabbix server bạn lại phải cấu hình lại từ việc add host đến add template và đặt trigger cảnh báo. Trong bài viết chắc chắn còn nhiều thiếu sót rất mong được sự góp ý của các bạn.

Chúc bạn thành công!

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>