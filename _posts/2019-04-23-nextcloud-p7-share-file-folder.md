---
title: "Nextcloud [Part 7] - Share file và folder"
categories:
  - Nextcloud
description: Nextcloud - Share file và folder
author: huytm
tags: [Nextcloud]
type: Document
set: Nextcloud
set_order: 7
---

## Mở đầu

Trong bài viết trước mình đã hướng dẫn các bạn cách tạo mới và upload file, folder. Trong bài viết này mình sẽ hướng dẫn chia sẻ file và folder trong cùng một tổ chức hoặc chia sẻ công khai đến nhiều người.

## Chia sẻ file và folder trong cùng tổ chức

Đăng nhập vào Nextcloud

<p align="center">
<img src="/images/img-nextcloud/part1/nextcloud1-8.png">
</p>

Chọn file hoặc folder muốn chia sẻ sau đó chọn **Biểu tượng chia sẻ**

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-1.png">
</p>

Một màn hình mới phía bên phải hiển thị ra, tại đây bạn sẽ nhập **User** hoặc **Group** trong cùng tổ chức của bạn và nhấn chia sẻ 

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-2.png">
</p>

Chia sẻ thành công

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-3.png">
</p>

#### Các tính năng mở rộng

- Mặc định khi thực hiện share thì user được share sẽ có quyền Edit file. Tùy vào nhu cầu bạn có thể chọn **Can edit** hoặc **không**

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-4.png">
</p>

- Mặc định khi thực hiện share user được quyền tiếp tục share cho người khác (**Can reshare**). Bạn có thể cho phép tính năng này hoặc không.

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-5.png">
</p>

- Bạn cũng có thể chọn **Set expriration date** để đặt hạn mức thời gian chia sẻ file. Hết hạn mức này. File sẽ không được share cho user đó nữa.

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-6.png">
</p>

- Bổ sung một thông điệp khi chia sẻ bằng tính năng **Note to recipient**

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-7.png">
</p>

- Dừng việc chia sẻ file (**Unshare**)

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-8.png">
</p>

## Public share

Trong trường hợp bạn muốn chia sẻ một file ra ngoài tổ chức, bạn có thể sử dụng tính năng **Share link**

Thực hiện share file như phía trên, sau đó chọn dấu **+** ở phần **Share link**.

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-9.png">
</p>

Sau đó chọn Copy link và gửi link này đến một người bạn cần chia sẻ file

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-11.png">
</p>


#### Ở phương pháp chia sẻ này bạn có thể chọn một số tính năng nâng cao như:

- **Allow Editting**: Cho phép chỉnh sửa file được chia sẻ.
- **Hide download**: Ẩn tính năng Download file được chia sẻ
- **Password protect**: Nhập password để có thể xem file được chia sẻ
- **Set expiration date**: Đặt ngày hết hạn của link chia sẻ
- **Note to recipient**: Thêm một thông điệp 
- **Delete share link**: Xóa share link, ngừng chia sẻ file.
- **Add another link**: Tạo một link chia sẻ mới. Sử dụng trong trường hợp bạn cần chia sẻ public đến nhiều người. Tuy nhiên có người chỉ được phép *Edit*, có người chỉ được phép *Download* chẳng hạn. Khi đó mỗi link sẽ có các tính năng nâng cao khác nhau và bạn chỉ việc copy link và gửi cho từng người thích hợp

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-10.png">
</p>

## Chia sẻ qua email

Nhập email cần chia sẻ sau đó chọn biểu tượng **Email** để chia sẻ file 

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-12.png">
</p>

#### Các tính năng mở rộng

- **Can edit**: Cho phép người được share chỉnh sửa file.
- **Password protect**: Nhập mật khẩu cho file được share.
- **Note to recipient**: Nhập một thông điệp khi chia sẻ file.
- **Unshare**: Dừng việc chia sẻ file. 

<p align="center">
<img src="/images/img-nextcloud/part4/nextcloud4-13.png">
</p>

## Tổng kết

Như vậy trong bài viết này, mình đã hướng dẫn các bạn cách share (chia sẻ) file hoặc folder theo 3 cách đó là:

- Chia sẻ trong cùng tổ chức 
- Chia sẻ khác tổ chức thông qua public link
- Chia sẻ khác tổ chức thông qua email

Chúc các bạn thực hiện thành công.

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
