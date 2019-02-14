---
date: 2019-02-14
title: "Để trở thành cao thủ LOGGING - Phần 4"
categories:
  - Logging
description: Tuyển tập LOGGING từ mầm non tới đại học
author: manhdv
tags: [Beginer, Linux, Logging]
type: Document
---

## 1. Chuyên mục điểm tin vắn ngày lễ Tình Nhân

Lại một Valentine nữa đã tới, chúc các bạn có một ngày lễ Tình Nhân ngọt ngào bên những dòng Log nhé ! <3 

Trước khi chuẩn bị socala và hoa hồng, hãy dành 1 phút để điểm lại nội dung của phần 3 nhé !

Những tiêu điểm cần nắm vững trong bài thứ 2 đó là : 
- Giới thiệu về Syslog : Syslog là gì? Syslog được phát triển như thế nào? Syslog được sử dụng như thế nào?
- Các cấu hình quan trọng trong syslog
- Các nguồn tạo log (Facility Level)
- Mức cảnh báo của Log (Severity Level)
- Cách xoay vòng log (Log Rotation)

Chúng ta sẽ tìm hiểu sâu hơn về Syslog với các nội dung : 

- Mô hình Log tập trung trong Linux.
- Cấu hình Log tập trung với Syslog.

## 2. Mô hình Log tập trung trong Linux.

![log](/images/img-logging/p4-log-00.png)

Tại sao lại phải sử dụng log tập trung?

- Do có nhiều nguồn sinh log

	 - Có nhiều nguồn sinh ra log, log nằm trên nhiều máy chủ khác nhau nên khó quản lý.
	 - Nội dung log không đồng nhất (Giả sử log từ nguồn 1 có có ghi thông tin về ip mà không ghi thông tin về user name đăng nhập mà log từ nguồn 2 lại có) -> khó khăn trong việc kết hợp các log với nhau để xử lý vấn đề gặp phải.
	 - Định dạng log cũng không đồng nhất -> khó khăn trong việc chuẩn hóa
	 - Đảm bảo tính toàn vẹn, bí mật, sẵn sàng của log.

- Do có nhiều các rootkit được thiết kế để xóa bỏ logs.
- Do log mới được ghi đè lên log cũ -> Log phải được lưu trữ ở một nơi an toàn và phải có kênh truyền đủ đảm bảo tính an toàn và sẵn sàng sử dụng để phân tích hệ thống.

Do đó lợi ích của log tập trung đem lại là

- Giúp quản trị viên có cái nhìn chi tiết về hệ thống -> có định hướng tốt hơn về hướng giải quyết
- Mọi hoạt động của hệ thống được ghi lại và lưu trữ ở một nơi an toàn (log server) -> đảm bảo tính toàn vẹn phục vụ cho quá trình phân tích điều tra các cuộc tấn công vào hệ thống
- Log tập trung kết hợp với các ứng dụng thu thập và phân tích log khác nữa giúp cho việc phân tích log trở nên thuận lợi hơn -> giảm thiểu nguồn nhân lực.

## 2. Cấu hình Log tập trung với Syslog.

![log](/images/img-logging/p4-log-01.png)

Máy Ubuntu chạy dịch vụ Web, là máy client gửi bản log về cho máy Ubuntun là Syslog server.

### Bước 1: Thực hiện trên Syslog server

Chỉnh sửa trong file cấu hình /etc/rsyslog.conf của máy chủ Syslog-server để nó có thể nhận các bản tin log từ các client gửi về.

Bỏ comment 2 dòng sau : 
```sh
$ModLoad imudp
$UDPServerRun 514
```

![log](/images/img-logging/p4-log-02.png)

Để máy chủ log tạo thành các thư mục lưu riêng log đối với từng máy Client gửi về thêm dòng này vào cuối file cấu hình. (Chú ý đặt trên GLOBAL DIRECTIVES)

```sh
$template TmplAuth,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log"
*.*     ?TmplAuth
```

![log](/images/img-logging/p4-log-03.png)

Phân quyền cho syslog để nó có thể tạo các file và thư mục trong /var/log

```sh
chown syslog.syslog /var/log
```

Restart service Rsyslog

```sh
systemctl restart rsyslog
```

### Bước 2: Thực hiện trên Syslog client

Thực hiện khai báo IP của Syslog server (dưới mục Rule)

```sh
*.*             @Syslog_IP:514
```

![log](/images/img-logging/p4-log-04.png)

Restart service Rsyslog

```sh
systemctl restart rsyslog
```

### Bước 3 : Kiểm tra trên Syslog server

Trên thư mục **/var/log/** của Syslog server sẽ xuất hiện thư mục log của Client. Thực hiện vào và kiểm tra log thu thập từ client.

![log](/images/img-logging/p4-log-05.png)

Như vậy là chúng ta đã lưu lại được log của client trên một server khác. Việc vận chuyển log sẽ được thực hiện liên tục tới khi kết nối network bị mất hoặc một trong 2 máy server, client có vấn đề

## 4. Chuyên mục tổng kết và rút kinh nghiệm 

Tại phần 4, chúng ta đã thực hiện các nội dung sau : 

- Tìm hiểu về Log tập trung
- Cách cấu hình log tập trung với Syslog trong môi trường Linux

## 5. Ở bài tiếp theo sẽ có gì?

Sau khi áp dụng phương pháp thu thập Log tập trung với Syslog, chúng ta đã giải quyết được một số vấn đề về việc lưu trữ cũng như quản lý log. 

Tuy nhiên, để khai thác hiệu quả các dữ liệu có trong log thì chúng ta cần có những công cụ cấp cao hơn. Trong vô vàn các giải pháp khai thác về Log thì mình vẫn luôn tin dùng Graylog, một giải pháp không còn mới, nhưng phù hợp với công việc và mang lại những trải nghiệm mới mẻ với mình. 

Trong những chuỗi bài tiếp theo, mình sẽ giới thiệu về giải pháp Graylog. Đồng thời, mình sẽ đưa ra các tình huống thực tế cần tới việc khai thác Log.

Xin cảm ơn và hẹn gặp lại mọi người trong những chuỗi bài tiếp theo !
