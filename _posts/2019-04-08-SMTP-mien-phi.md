---
title: SMTP miễn phí
  - Linux
description: SMTP miễn phí
author: canhdx
tags: [Beginer, Other, SMTP]
type: Document
---

## SMTP Server 

Mọi thao tác liên quan đến email đều sử dụng SMTP Server

SMTP Server là một dịch vụ gửi mail số lượng lớn mà không bị giới hạn như các gói email kèm theo hosting hay Gmail. 

Chúng ta có thể cài đặt một Server mail dùng để gửi mail, thuê 1 email Server để gửi mail, ... nhưng một vấn đề thường xảy ra đối với các giải pháp email phía trên đó là 
- Quá tải Server 
- IP Share hosting bị blacklist
- Quản lý các dịch vụ phức tạp 
- Và còn nhiều vấn đề khác phát sinh trong quá trình vận hành

Một số nhà cung cấp còn cung cấp giải pháp **Email Marketing**

## Một số nhà cung cấp SMTP Server miễn phí

Một vài nhà cung cấp cho phép chúng ta sử dụng SMTP Server miễn phí trong một giới hạn nhất định 

- <a href="https://support.google.com/a/answer/166852?hl=en&ref_topic=28609" target="_blank">Gmail:</a>
	+ 500 email/day
	+ Sender Domain gói free: Không (Cần sử dụng Gsuite)
- <a href="https://www.smtp2go.com/pricing/" target="_blank">SMTP2Go:</a> 
	+ 1.000 email/mon
	+ Sender Domain gói free: Có 
- <a href="https://www.sparkpost.com/pricing/" target="_blank">Sparkpost: </a>
	+ 500 email/day
	+ 15.000 email/mon
	+ Sender Domain gói free: Có
- <a href="https://mailchimp.com/pricing/" target="_blank">Mailchimp:</a> 
	+ 12.000 email/mon
	+ Sender Domain gói free: 
- <a href="https://www.mailgun.com/pricing" target="_blank">Mailgun:</a> 
	+ 100 email/hours
	+ 10.000 email/mon
	+ Sender Domain gói free: Không
- <a href="https://sendgrid.com/pricing/" target="_blank">SendGrid:</a> 
	+ 40.000 email/mon
	+ Sender Domain gói free: Không
- <a href="https://sendpulse.com/prices" target="_blank">SendPulse</a>
	+ 12.000 email/mon
	+ Sender Domain gói free: Không
- Các SMTP server khác 

## Mục đích cơ bản 
Sử dung SMTP để cấu hình cho các application có tính năng gửi email. Trong bài viết này chúng tôi sẽ hướng dẫn cấu hình SMTP trên SMTP2GO

## Đăng ký tài khoản tại SMTP2Go 
<p align="center">
<img src="/images/img-smtp/smtp2go-01.png">
</p>
> Lưu ý nên sử dụng email dạng email doanh nghiệp để có thể gửi mail ổn định canhdx@company.vn

Login vào tài khoản 
<p align="center">
<img src="/images/img-smtp/smtp2go-02.png">
</p>

Truy cập `Settings` --> `Users` --> `Add SMTP User`
<p align="center">
<img src="/images/img-smtp/smtp2go-03.png">
</p>

Bổ sung thông tin username cho SMTP
<p align="center">
<img src="/images/img-smtp/smtp2go-04.png">
</p>

Các thông tin đăng nhập SMTP sẽ bao gồm 
<p align="center">
<img src="/images/img-smtp/smtp2go-05.png">
</p>

```sh 
SMTP Server: mail.smtp2go.com
SMTP Port: 2525
Alternative ports: 8025, 587, 80 or 25. TLS is available on the same ports.
SSL is available on ports 465, 8465 and 443
```

Kiểm tra thông tin SMTP vừa tạo <a href="https://www.smtper.net/" target="_blank">tại đây</a>
<p align="center">
<img src="/images/img-smtp/smtp2go-06.png">
</p>


Kiểm tra hộp thư đến 
<p align="center">
<img src="/images/img-smtp/smtp2go-07.png">
</p>

Hoặc có thể sử dụng Script sau trên Linux(CentOS or Ubuntu) để kiểm tra 
```sh 
wget -O https://raw.githubusercontent.com/nhanhoadocs/scripts/master/Utilities/smtp_test.py smtp_test.py
```

Test SMTP vừa tạo 
```sh 
python smtptest.py --debuglevel 1 --usetls --port=2525 -u demo1  -p bjY0MHQ0NW**** "CanhDX <canhdx@cloudchuanchi.com>" canhdx@nhanhoa.com.vn mail.smtp2go.com
```

Kết quả
<p align="center">
<img src="/images/img-smtp/smtp2go-07'.png">
</p>


## Custom Domain cho SMTP trên SMTP2GO

Như email nhận được từ SMTP thì email bạn gửi đi sẽ gửi dưới tên là email bạn đăng ký SMTP

Chuẩn bị một domain mà bạn muốn cấu hình ở đây của mình là `cloudchuanchi.com`

Truy cập `Settings` --> `Add Domains`
<p align="center">
<img src="/images/img-smtp/smtp2go-08.png">
</p>

Thông tin các bản ghi được tạo ra 
<p align="center">
<img src="/images/img-smtp/smtp2go-09.png">
</p>

Đăng nhập vào trang <a href="https://zonedns.vn/" target="_blank">quản trị DNS</a> của domain để thực hiện add các bản ghi Verify domain này chính xác là của bạn 
<p align="center">
<img src="/images/img-smtp/smtp2go-10.png">
</p>

Quay lại SMTP2GO thực hiện Verify các record 
<p align="center">
<img src="/images/img-smtp/smtp2go-11.png">
</p>

Verify thành công domain
<p align="center">
<img src="/images/img-smtp/smtp2go-12.png">
</p>

Domain list 
<p align="center">
<img src="/images/img-smtp/smtp2go-13.png">
</p>

Gửi email với Sender là domain chưa được Verify 
<p align="center">
<img src="/images/img-smtp/smtp2go-14.png">
</p>

Gửi email với Sender là domain đã được Verify 
<p align="center">
<img src="/images/img-smtp/smtp2go-15.png">
</p>

## Tổng kết

Đối với các SMTP Server miễn phí, Thao tác sử dụng SMTP và xác thực các domain để gửi từ domain này tương tự các bước mình đã hướng dẫn phía trên
- Tạo tài khoản 
- Tạo SMTP user, App 
- Kiểm tra họat động của SMTP 
- Xác thực Domain 
- Gửi email với domain đã được xác thực 

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>