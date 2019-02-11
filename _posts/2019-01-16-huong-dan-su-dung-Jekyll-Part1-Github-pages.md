---
title: "Hướng dẫn sử dụng Jekyll - Part 1: Github Pages"
categories:
  - Other
description: Tài liệu hướng dẫn sử dụng Jekyll, Phần 1 - Github Pages
author: huytm
tags: [Jekyll, Centos]
type: Document
---

## Lời mở đầu
Bạn là một SysAdmin - Bạn là Developer và muốn ghi chú lại tài liệu của mình? Hay bạn đơn giản chỉ là một người thích viết lách và có một chút ít kiến thức ít ỏi khi sử dụng git và Github (như mình :v) ?

Bạn hoàn toàn có thể sử dụng một nền tảng có sẵn đề  viết biết blog  như [blogspot](https://www.blogger.com/), [wordpress](https://wordpress.com){:target="_blank"}, [netlify](https://www.netlify.com/){:target="_blank"} hay vân vân và mây mây. Nhưng bạn có biết bản thân Github cũng có một nền tảng hỗ trợ bạn publish một blog không? Đó là một nền tảng khá nổi tiếng, có cộng đồng phát triển lớn, đã support tương đối đầy đủ tính năng để bạn có thể phát triển một trang blog cho riêng mình. Là một  nền tảng không cầu kỳ, nhỏ gọn, customize dễ dàng (nếu bạn biết một chút về html, css) và có performace khá tốt. Và nền tảng mình muốn nói tới ở đây chính là [Github Pages](https://pages.github.com/){:target="_blank"}.

Trong phạm vi bài viết này mình không so sánh sự khác biệt giữa các nền tảng dùng để viết blog, cái nào tốt hơn cái nào, cái nào đẹp hơn cái nào mà mình chỉ  viết một tài liệu nho nhỏ cho bản thân, cũng như chia sẻ với những ai muốn sử dụng Github Pages nhưng chưa biết bắt đầu từ đâu.

Bài viết gồm bốn phần chính là:
- Giới thiệu về Github Pages.
- Cơ chế hoạt động của Github Pages
- Tự tạo một blog với Github Pages.
- Sử dụng một theme có sẵn cho blog.

## 1. GIỚI THIỆU VỀ GITHUB PAGES

_Github pages là một web hosting service được phát triển bởi Github để lưu trữ các trang web tĩnh phục vụ cho việc viết blog, tài liệu cho dự án, hoặc thậm chí là viết sách_ (Nguồn wiki).

Github pages chủ yếu dựa trên nền tảng phần mềm [Jekyll](https://jekyllrb.com/){:target="_blank"} - một nền tảng viết bằng Ruby được phát triển bởi [Tom Preston-Werner](https://en.wikipedia.org/wiki/Tom_Preston-Werner){:target="_blank"}. Jekyll release phiên bản đầu tiên của mình năm 2008, và sau đó được Parker Moore tiếp quản. Tính đến năm 2017, Jekyll đã là trình tạo web tĩnh phổ biến nhất thế giới và phần lớn là do Github áp dụng.

>Có thể bạn biết rồi: cha đẻ của Jekyll là Tom Preston-Werner cũng chính là người đồng sáng lập ra Github.

Tính đến thời điểm tháng 04/01/2019. Jekyll đã release đến phiên bản 3.8.5 và Github pages sử dụng phiên bản stable-Jekyll là 3.7.4.

Cộng đồng của Jekyll cũng rất lớn, khoảng 8000 contributor chính thức và hàng nghìn người sử dụng ở khắp nơi trên thế giới. Điều đó cũng có thể đánh giá rằng, Jekyll là một sản phầm đáng để sử dụng đấy chứ.



<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/github-jekyll.png" title="Nguồn whatis.techtarget.com">
</p>
<p align="center">
<span>Github của Jekyll</span>
</p>

>Share link github của Jekyll cho ai muốn trở thành thánh Jekyll (https://github.com/jekyll/jekyll)

## 2. CƠ CHẾ HOẠT ĐỘNG CỦA GITHUB PAGES

Để  hiểu về cơ chế hoạt động của Github pages trước hết bạn cần phải biết một chút về các khái niệm Ruby, RubyGem, Git, Repository, Branch.
- **[Ruby](https://www.ruby-lang.org/vi/){:target="_blank"} :** là một ngôn ngữ lập trình nguồn mở, chú trọng cao về hiệu suất. Jekyll được viết bằng Ruby và sử dụng Ruby để thông dịch các file mã nguồn ra html.
- **RubyGems :** là một hệ thống quản lý thư viện (libs-packages-managers) để quản lý các thư viện, các plugin được viết bằng Ruby. Nó cũng tương đương với **npm** của javascript, **mvn** của java, hay **composer** của php.
- **Git :** là hệ thống quản lý mã nguồn phân tán, cha đẻ của Git là **Linus Torvalds** (thánh này ai làm về IT mà không biết thì quả là có lỗi với thánh :laughing::laughing:. Đây chính là thánh viết ra **Linux Kernel** rồi kết hợp với dự án **GNU** tạo nên hệ điều hành làm mưa làm gió `GNU/Linux`).
- **Repository :** Đúng như tên gọi của nó, Repository là một kho chứa. Trong hệ thống quản lý mã nguồn Git, Repository chính là một project, một dự án nơi mọi người phát triển và tập trung code của mình tại đó.
- **Branch :** Trong repository lại chia thành nhiều nhánh. Việc chia nhánh này giúp cho việc quản lý mã nguồn được tốt hơn, ngoài ra còn có các tác dụng khác, tùy vào cách sử dụng của mỗi người. Trong quy trình phát triển phần mềm - đa số, mỗi nhánh gồm các nhiệm vụ khác nhau. Chẳng hạn **master** là một nhánh xuyên suốt sẽ là nhánh để release sản phầm - là nhánh sẵn sàng deploy nhất, nhánh **develope** sử dụng để phát triển, hoặc có những nhánh chỉ để phát triển riêng một tính năng cho sản phẩm chẳng hạn...

Mặc định **Github pages sử dụng repository và mã nguồn ở nhánh master** để triển khai blog.

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/model.png">
</p>

## 3. TỰ TẠO MỘT BLOG VỚI GITHUB PAGES

**1. Đăng ký một tài khoản Github và đăng nhập.**

**2. Tạo một repository chứa mã nguồn.**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/blank-step1.png">
</p>

**3. Chọn tạo "New" để tạo mới.**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/blank-step2.png">
</p>

**4. Đặt tên cho Repository và chọn "Create repository".**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/blank-step3.png">
</p>

**5. Tạo một file README để mô tả sơ qua về Repository của bạn.**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/blank-step4.png">
</p>

**6. Nhập mô tả cho Repository vào file README sau đó chọn "Commit new file".**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/blank-step5.png">
</p>

**7. Tạo file "index.html".**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/blank-step6.png">
</p>

**8. Nhập nội dung file - vì file có phần mở rộng là html, nên nội dung file sẽ được viết bằng ngôn ngữ html.**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/blank-step7.png">
</p>

**9. Publish blog thôi nào !!.**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/blank-Step8.png">
</p>

**10. Đổi tên repository thành định dạng như sau "any-name.github.io". Trong bài viết này mình để là "huytm.github.io".**

 >Một lưu ý nho nhỏ, nếu trong tài khoản github của bạn đã có một repository đã deploy Github Page rồi, thì khi các repository khác khi  deploy tiếp lên Github Pages sẽ là một **URI** của **URL** đã đăng ký trước đó. Ví dụ "http://huytm.github.io/my-another-blog"

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/blank-step9.png">
</p>

**11. Kéo xuống một chút bạn sẽ thấy thông báo như sau là thành công rồi đó**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/blank-step10.png">
</p>

**12. Truy cập url mà đã lấy được từ bước 11 để xem thành quả**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/blank-step13.png">
</p>

 **Tadaaa** :v:

## 4. SỬ DỤNG  MỘT  THEME CÓ SẴN  CHO BLOG

Thường với một người mù mờ về nghệ thuật và code kém như mình thì mình sẽ hay chọn một theme có sẵn cho blog :laughing: :laughing:

Để chọn một theme bất kỳ bạn vào trang này https://jekyllthemes.io/free. Sau đó lựa chọn theme bất kỳ mà mình thích nhé, lưu ý tìm được github của theme đó thì càng tốt :laughing:

Giả sử mình chọn được **theme** này https://deanattali.com/beautiful-jekyll/ và **github** của nó là https://github.com/daattali/beautiful-jekyll

**1. Đầu tiên hãy folk nó về để nó rẽ nhánh thành một repository của mình đã nhé.**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/step1.png">
</p>

**2. Folk xong nó sẽ trông thế này.**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/step2.png">
</p>

**3. Tiếp theo hãy mô-đi-phê nó để nó trở thành blog của mình.**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/step3.png">
</p>

**4. Sửa chỗ bôi đỏ này là quan trọng nhất này.**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/step4.png">
</p>

**5. Sửa xong thì vào "Setting" trỏ lại cái url cho nó giống như ở hướng dẫn trên.**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/step5.png">
</p>

**6. Đổi lại tên repo để nó trỏ về url của mình.**

<p align="center">
<img src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/step6.png">
</p>

**7. Chờ tí cho code đồng bộ lên Github Pages... Sau đó truy cập vào url.**

<p align="center">
<img  src="https://raw.githubusercontent.com/huytm/How-to-Jekyll/master/images/step7.png">
</p>

**Done** :smiley::smiley:

## TỔNG KẾT

- Bài viết này chỉ là một hướng dẫn nho nhỏ để các bạn sử dụng **Github Pages** viết blog cho riêng mình.
- Để modify những theme có sẵn hay tự build một blog từ đầu thì còn **phụ thuộc vào khả năng của từng người**.
- Bài viết cũng là bước tiếp cận đầu tiên, giúp cho các bạn có cái nhìn tổng quan khi sử dụng Github Pages và Jekyll.
- Bài viết có thể còn có sai sót, nên mình rất mong nhận được sự đóng góp ý kiến từ các bạn.
- Trong bài [tiếp theo](https://blog.cloud365.vn/other/huong-dan-su-dung-Jekyll-Part2-install-jekyll-centos7/){:target="_blank"} mình sẽ viết hướng dẫn deploy Blog trên một con **Local Jekyll** mà không cần phải phụ thuộc vào Github Pages nữa.

Mình xin dừng phím tại đây, cảm ơn các bạn đã đọc đến tận đây nhé :D.

>"if you have knowledge let others light their candles in it"

### TÀI LIỆU THAM KHẢO

https://en.wikipedia.org/wiki/Jekyll_(software)

https://en.wikipedia.org/wiki/Tom_Preston-Werner

https://github.com/jekyll/jekyll

https://jekyllrb.com/news/releases/

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>

