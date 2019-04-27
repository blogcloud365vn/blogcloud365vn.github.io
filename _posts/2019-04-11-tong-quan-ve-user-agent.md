---
date: 2019-04-11
title: "Tổng quan về chuỗi nhận dạng trình duyệt (Browser’s User Agent)"
categories:
  - Linux
description: Tổng quan về chuỗi nhận dạng trình duyệt (Browser’s User Agent)
author: thanhnb
tags: [Linux, CentOS]
type: Document
---

## Tổng quan

Nếu bạn thường xuyên lướt Web chắc bạn đã từng thắc mắc làm sao các trang Web có thể nhận dạng bạn sử dụng trình duyệt nào, hệ điều hành nào, đang sử dụng điện thoại hay máy tính cá nhân?

Ví dụ mình sử dụng chrome truy cập vào trang Web `https://www.whatismybrowser.com/`

Kết quả:

![](/images/img-tong-quan-chuoi-user-agent/chrome.png)

Và khi mình chuyển sang trình duyệt Firefox, truy cập lại địa chỉ `https://www.whatismybrowser.com/`

Kết quả:

![](/images/img-tong-quan-chuoi-user-agent/firefox.png)

Sau đây mình sẽ giải thích cách mà các trang Web có nhận diện được trình duyệt của bạn. Để các trang Web có thể nhận diện được trình duyệt của bạn, chúng sẽ sử dụng chuỗi 'user agent' của trình duyệt để xác định. Nói đơn giản, khi trình duyệt của bạn kết nối tới 1 trang Web chúng sẽ gửi một chuỗi nhận dạnh nó theo các request và tất nhiên, bạn hoàn toàn có thể thay đổi chuỗi nhận dạng này.

## Cơ bản

Khi trình duyệt của bạn kết nối tới một trang Web, nó sẽ thêm vào chuỗi 'User-Agent' vào trong HTTP Header. Nội dung của chuỗi 'User Agent' sẽ khác nhau giữa các trình duyệt. Bên cạnh đó, mỗi trình duyệt đều có chuỗi 'user agent' riêng để định danh. Cơ bản hơn, chuỗi user agent là giống như 'LỜI GIỚI THIỆU' của trình duyệt khi bắt đầu cuộc 'NÓI CHUYỆN' với Web Server. VD: 'Chào Bạn, Mình là Chrome, mình đang chạy trên hệ điều hành Linux'.

Web Server có thể sử dụng thông tin nhận được từ chuỗi 'user agent' để thay đổi cách giao tiếp giữa nó và trình duyệt. VD: Nếu người dùng đang sử dụng trình duyệt của điện thoại, Web Server sẽ sử dụng giao diện giành riêng cho điện thoại để cải thiện hiệu năng.

## Ví dụ về chuỗi User Agent

Ví dụ:
```
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36
```

Phân tích chuỗi:

- Trình duyệt sử dụng: Chrome 60
- Tên trình duyệt: Chrome
- Code trình duyệt: chrome
- Phiên bản sử dụng: 60
- Phiên bản đầy đủ: 60.0.3112.113
- Loại trình duyệt 'web-browser' dựa theo thông tin (`Windows NT 10.0; Win64; x64`)
- Hệ điều hành: Windowns 10
- Phiên bản hệ điều hành: NT 10.0

## Lịch sử ra đời chuỗi User Agent

Mosaic là trình duyệt Web đầu tiên. User Agent nó sử dụng là `NCSA_Mosaic/2.0`. Sau đó, trình duyệt Mozilla được phát hành (Ban đầu có tên là Netscape sau đó được đổi tên thành Firefox), ban đầu Mozilla sử dụng chuỗi User Agent `Mozilla/1.0`. Mozilla tại thời điểm công bố được đánh giá là trình duyệt cao cấp (hơn Mosaic) vì hỗ trợ `frames`. Và các nhà phát triển Web Server chỉ hỗ trợ frame cho trình duyệt Mozilla, các trình duyệt Web khác thì không.

Sau thời điểm Netscape (Khi đó trình duyệt Netscape chưa được đổi tên thành Mozilla) công bố, Microsoft phát hành trình duyệt Internet Explorer, hi vọng nó sẽ là 'Netscape Killer' và đây cũng là trình duyệt hỗ trợ frame. Tuy nhiên, IE hỗ trợ frame nhưng lại không nhận được các frame từ Web Server vì khi đó các nhà phát triển Web Server chỉ support cho frame cho Netscape. Nóng lòng vì phải chờ đợi các nhà phát triển Web Server tìm hiểu về công nghệ frame của IE, Microsoft tự thêm chuỗi `Mozilla/1.22` vào trình duyệt của mình và phát trình duyệt IE tương thích với frame của Mozzila. Từ đó trình duyệt IE có thể nhận được frame giống như Netscape.

Và đúng như mong đợi của Microsoft, IE nổi lên, vượt qua và đánh bại trình duyệt Netscape. Nhưng sau đó, trình duyệt Netscape được tái sinh với tên Mozilla. Mozilla phát triển Gecko, sử dụng chuỗi User Agent `Mozilla/5.0 (Windows; U; Windows NT 5.0; en-US; rv:1.1) Gecko/20020826`. Sau này Mozilla được đổi tên thành Mozilla Firefox, chuỗi User Agent được đổi thành `Mozilla/5.0 (Windows; U; Windows NT 5.1; sv-SE; rv:1.7.5) Gecko/20041108 Firefox/1.0`. Gecko Engine rất tốt, hỗ trợ tốt cho việc phát triển Web, trong khi đó các trình duyệt khác thì không.

Công đồng Linux cũng phát triển engine `KHTML` nhưng không được hỗ trợ tốt như Gecko, vì vậy họ bổ sung vào chuỗi User Agent `Mozilla/5.0 (compatible; Konqueror/3.2; FreeBSD) (KHTML, like Gecko)` và phát triển tương thích với frame của Gecko.

Apple sau này xây dựng trình duyệt Safari, sử dụng engine KHTML và tách thành 1 bản forked riêng, bổ sung thêm nhiều tính năng. Sau đó gọi engine của mình là `WebKit`. Chuỗi User Agent của Safari sử dụng `Mozilla/5.0 (Macintosh; U; PPC Mac OS X; de-de) AppleWebKit/85.7 (KHTML, like Gecko) Safari/85.5`.

Cuối cùng, Google phát triển Chrome. Chrome phát triển sử dụng `WebKit` mà `WebKit` lại phát triển dựa trên `KHTML`, `KHTML` phát triển tương thích với `Gecko`, cuối cùng tất cả các trình duyệt đều phát triển để trở thành `Mozilla`. Chrome sử dụng chuỗi User Agent `Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/525.13 (KHTML, like Gecko) Chrome/0.2.149.27 Safari/525.13`.

Tới thời điểm hiện tại, các trình duyệt đều phát triển để giống một trình duyệt khác, dần dần chuỗi User Agent trở nên lộn xộn gây khó hiểu nhầm lẫn

## Tổng kết

Đến đây mình đã giới thiệu tới các bạn tổng quan về chuỗi User Agent cũng như lịch sử phát triển cuả nó.

Cám ơn các bạn đã quan tâm.

## Nguồn 

https://webaim.org/blog/user-agent-string-history/

https://en.wikipedia.org/wiki/Gecko_(software)

https://www.whatismybrowser.com/

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>