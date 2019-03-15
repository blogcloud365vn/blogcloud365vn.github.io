---
title: Sử dụng VScode để làm việc với GitHub
categories:
  - Other
description: Sử dụng VScode để làm việc với GitHub
author: canhdx
tags: [Beginer, Linux, Git, Other]
type: Document
---

## Nguyên tắc hoạt động

Ở bài hướng dẫn trước các bạn đã biết cách thao tác cơ bản với Git, hiểu thế nào là Git Server và Git Local. Làm thế nào để Git Local hoạt động được với Git Server. Hôm nay tôi sẽ giới thiệu cho bạn một Git Server nổi tiếng miễn phí mà có lẽ bạn cũng có biết đó là Github

<p align="center">
<img src="/images/img-github/github.png">
</p>

Github là một Git Repository miễn phí được sử dụng rộng rãi trên toàn thế giới. Tại đây bạn có thể  làm vô số chuyện với nó:
- Bạn cần nơi quản lý, lưu trữ sourcode của bạn --> Hãy sử dụng Github, bạn có thể lưu trữ Public hoặc Privated
- Bạn cần tìm kiếm các hướng dẫn về vấn đề nào đó --> Hãy tìm kiếm thử trên Github, có thể đã có tiền bối nào đó từng làm qua và docs lại kinh nghiệm để chia sẽ. Điều này sẽ tiết kiệm cho ban rất nhiều thời gian tìm hiểu.
- Bây giờ bơi ra biển lớn bạn có thể sử dụng các mã nguồn được public trên mạng bằng cách clone về sử dụng
- Gặp vấn đề trong lúc sử dụng phần mềm trên ư --> Hãy lên Github để trao đổi và report lại các issue bạn gặp phải trong quá trình sử dụng phần mềm đó. Bạn đang góp phần phát triển phần mềm đó đấy. 
- Thậm chí bạn có thể chỉnh sửa trực tiếp các mã nguồn và push lại vào mã nguồn đó nếu bạn có khả năng fix các issue phía trên. 
- Số repos trong profile của bạn cũng là một trong những tiêu chí cho nhà tuyển dụng đánh giá độ active của bạn trong cộng đồng nói chung và năng lực bản thân bạn nói riêng 

## Đăng ký tài khoản 

Nhưng việc đầu tiên vẫn là phải đăng ký tài khoản tại trang chủ <a href="https://github.com" target="_blank">Github</a>

<p align="center">
<img src="/images/img-github/github1.png">
</p>

Việc đăng ký tài khoản cực kỳ đơn giản bạn chỉ cần nhập username/password và địa chỉ email. Sau khi đăng ký xong bạn cần vào Email kích hoạt tài khoản thế là xong. 

Xác thực capcha

<p align="center">
<img src="/images/img-github/github2.png">
</p>

Chọn gói dịch vụ 

<p align="center">
<img src="/images/img-github/github3.png">
</p>

Hoàn tất đăng ký tài khoản 

<p align="center">
<img src="/images/img-github/github4.png">
</p>

Verify email

<p align="center">
<img src="/images/img-github/github6.png">
</p>

## Tạo Github repository 

Sau khi đăng ký xong tài khoản Github và đăng nhập. Bạn có thể tạo một Github Repository. 

<p align="center">
<img src="/images/img-github/github5.png">
</p>

Đặt tên cho Repo và tạo 

<p align="center">
<img src="/images/img-github/github7.png">
</p>

- Tên Repo là duy nhất trong tài khỏan của bạn 
- Mô tả về Repo 
- `Public`: Mọi người có thể thấy repo của bạn hoặc `Private`: Chỉ mỗi bạn thấy repo này
- Tự động tạo file `README.md` mô tả về repo 

Tạo hoàn tất 

<p align="center">
<img src="/images/img-github/github8.png">
</p>

## Clone repo về máy 

Lấy đường dẫn Repo 

<p align="center">
<img src="/images/img-github/github09.png">
</p>

Sử dụng Git để clone repo về máy 
```
git clone https://github.com/CloudNhanhoa/repo1.git
```

## Download và cài đặt Visual Studio Code (VScode)

Chúng ta có thể sử dụng Git để thao tác trên PC hoặc sử dụng VScode để thuận tiện hơn trong việc Dev cũng như thao tác với Sourcecode 

<a href="https://code.visualstudio.com/download" target="_blank">Download VScode</a> và cài đặt VScode lên máy tính

## Sử dụng VScode làm việc với Github

Mở VScode `File` --> `Open Folder` chọn folder vừa clone về  chọn `Open`

<p align="center">
<img src="/images/img-github/github10.png">
</p>


Thực hiện chỉnh sửa README.md và thêm file trên VScode chúng ta có thể thấy những file chỉnh sửa ở đây 

<p align="center">
<img src="/images/img-github/github11.png">
</p>

VScode hỗ trợ các thao tác nhanh đối với git 

Git add chọn `YES` để  add các file thay đổi 

<p align="center">
<img src="/images/img-github/github12.png">
</p>

Đặt commit name bấm `ENTER`

<p align="center">
<img src="/images/img-github/github13.png">
</p>

Sau khi commit chúng ta có thể thấy được không còn file nào thay đổi 

<p align="center">
<img src="/images/img-github/github14.png">
</p>

Thực hiện thao tác push lên repo tương ứng 

<p align="center">
<img src="/images/img-github/github15.png">
</p>

Đăng nhập Username và password của repo 

<p align="center">
<img src="/images/img-github/github16.png">
</p>
<p align="center">
<img src="/images/img-github/github17.png">
</p>

Kiểm tra repo trên Github để thấy các thay đổi chúng ta vừa cập nhật 

<p align="center">
<img src="/images/img-github/github18.png">
</p>

## Clone tài liệu từ Repo khác 

Truy cập repo cần clone tài liệu và bấm `Fork`

<p align="center">
<img src="/images/img-github/github19.png">
</p>

Quá trình `fork` (clone) tài liệu sẽ tạo một bản sao của repo này trên tài khoản của bạn 

<p align="center">
<img src="/images/img-github/github20.png">
</p>

Kiểm tra tài liệu trong repo của bạn chúng ta có thể thấy tài liệu vừa `Fork`

<p align="center">
<img src="/images/img-github/github21.png">
</p>

Lúc này sourcecode trên repo này chúng ta đã có hoàn toàn quyền chỉnh sửa bổ sung trên đó. Thao tác clone và chỉnh sửa giống như phía trên. 

## Clone tài liệu từ Repo khác 

Bài viết chỉ hướng dẫn cơ bản cách đăng ký và sử dụng VScode để làm việc cơ bản với Github. Các tính năng của Github bạn có thể tự bổ sung trong quá trình làm việc. Hy vọng bài viết có thể hữ 

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>

