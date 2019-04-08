---
date: 2019-04-08
title: Hướng dẫn fix lỗi bảo mật Apache Web Server trong 
categories:
  - Linux
description: Hướng dẫn kiểm tra và fix lỗi bảo mật nghiêm trọng từ Apache Web Server ảnh hưởng tới control panel hosting DirectAdmin.
author: duydm
tags: [Linux, Apache]
type: Document
---

## 1. Apache Web Server là gì?

Apache hay là chương trình máy chủ HTTP là một chương trình dành cho máy chủ đối thoại qua giao thức HTTP. Apache chạy trên các hệ điều hành tương tự như Unix, Microsoft Windows, Novell Netware và các hệ điều hành khác. `Apache` đóng một vai trò quan trọng trong quá trình phát triển của mạng web thế giới (World Wide Web). Phiên bản phát hành đầu tiên vào tháng 4 năm 1995 bởi Apache Software Foundation. <a href="https://vi.wikipedia.org/wiki/Apache_(HTTP)" target="_blank">(Wikipedia - Apache)n</a>. Phần lớn các máy chủ web đang hoạt động hiện nay đang sử dụng `Apache`.

![](/images/img-apache-da/apache-server.png)

Apache web server là một lựa chọn hợp lý để vận hành website một cách ổn định và có thể tùy chỉnh linh hoạt.

+ Là phần mềm mã nguồn mở <br>
+ Được đội ngũ phát triển cập nhật, vá lỗi bảo mật liên tục.<br>
+ Dễ dàng cấu hình và sử dụng.<br>
+ Hoạt động đa nền tảng (Unix và Windows).<br>
+ Hoạt động hiệu quả với website WordPress.<br>
+ Tin cậy và ổn định.<br>

## 2. Lỗ hổng bảo mật Apache Web Server CVE-2019-0211 

Thời gian gần đây trên các kênh thông tin liên tục nhận được những thông tin cảnh báo bảo mật về lỗ hổng quan trọng trong phần mềm `Apache HTTP Server`. Cảnh báo được `Mark J Cox`, một trong những thành viên sáng lập của Quỹ phần mềm `Apache` và dự án `OpenSSL` đưa ra.

![](/images/img-apache-da/lo-hong-bao-mat.jpg)

Lỗ hổng ảnh hưởng đến Apache HTTP Server phiên bản 2.4.17 đến 2.4.38 và có thể cho phép bất kỳ người dùng ít đặc quyền thực thi mã tùy ý với quyền root trên máy chủ mục tiêu (hình thức tấn công leo thang đặc quyền). Lỗ hổng ảnh hưởng nhiều đến các dịch vụ lưu trữ web chia sẻ (shared hosting), cho phép kẻ tấn công với khả năng thực thi các tập lệnh PHP hoặc CGI trên trang web có được quyền truy cập là `root` trên máy chủ. Lỗi này do MPM (Multi-Processing Module) prefork, event, worker thực thi dưới dạng child process (tiến trình con) bao gồm các tiến trình thực thi bởi một process chạy với quyền cao nhất `root` , lỗi sẽ xảy ra khi apache restart theo kiểu (`httpd graceful`) mục tiêu cuối cùng là truy cập được vào cơ sở dữ liệu của website khác được lưu trữ trên cùng máy chủ.

Ngay sau khi CVE-2019-0211 được công bố các nhà phát triển của Apache đã đưa ra phiên bản  Apache httpd 2.4.39, ở phiên bản này cập nhật thêm 2 lỗi bảo mật khác liên quan tới  “người dùng có thông tin xác thực hợp lệ để xác thực bằng tên người dùng khác, bỏ qua các hạn chế kiểm soát truy cập được định cấu hình” (CVE-2019-0217) và lỗ hổng mod_ssl kiểm soát bỏ qua truy cập (CVE-2019-0215), “một lỗi trong mod_ssl khi sử dụng xác minh chứng chỉ ứng dụng khách theo vị trí với TLSv1.3 cho phép khách hàng hỗ trợ Post-Handshake Authentication được cấu hình để bỏ qua các hạn chế kiểm soát truy cập được định cấu hình.”

## 3. Fix lỗi CVE-2019-0211 control panel hosting DirectAdmin

### 3.1. Update custombuild 

**Check version custombuild**

```
cd /usr/local/directadmin/custombuild
./build version
```

![](/images/img-apache-da/Screenshot_1290.png)

**Với các server đang sử dụng Custombuild 1.x cần phải update lên 2.x**

```
cd /usr/local/directadmin
mv custombuild custombuild_1.x
wget -O custombuild.tar.gz http://files.directadmin.com/services/custombuild/2.0/custombuild.tar.gz
tar xvzf custombuild.tar.gz
cd custombuild
./build
```

### 3.2. Update apache 2.4

**Check version apache**

```
httpd -v
```

![](/images/img-apache-da/Screenshot_1291.png)

**Với các server cần update từ apache 2.2 lên apache 2.4**

```
cd /usr/local/directadmin/custombuild
./build set apache_ver 2.4
./build update
./build clean
./build apache n
./build php n
./build rewrite_confs
service httpd restart
```

### 3.3. Update apache version latest 2.4.39

**Thực hiện các câu lệnh sau để cập nhật apache version latest 2.4.39**

```
cd /usr/local/directadmin/custombuild
./build update
./build apache
```

![](/images/img-apache-da/Screenshot_1292.png)

![](/images/img-apache-da/Screenshot_1293.png)

**Check lại version apache**

```
httpd -v
```
![](/images/img-apache-da/Screenshot_1294.png)

Như vậy là bạn đã update phiên phiên bản apache lên version lastest 2.4.39 để fix lỗi bảo mật CVE-2019-0211.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>