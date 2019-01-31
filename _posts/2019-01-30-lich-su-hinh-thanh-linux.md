---
title: "Lịch sử hình thành Linux"
categories:
  - Other
description: Lịch sử hình thành Linux
author: huytm
tags: [Linux]
type: Document
---

## Mở đầu


**Back to basic - có thể bạn biết rồi hoặc có thể bạn chưa biết =), nhưng thôi cứ đọc, có khi sẽ có thêm thông tin hữu ích cho bản thân mình nhé**

Tại sao mình lại viết bài này?

Nhắc đến **Linux** thì cũng đã có thời gian đầu khi đi làm mình bị confused giữa Linux và Unix. Bản thân các bạn làm SysAdmin đôi lúc cũng sẽ quên hoặc bị nhầm lẫn. Vậy Unix và Linux khác nhau thế nào và lịch sử hình thành Linux ra sao, mình sẽ bắt đầu kể một câu chuyện như thế này:

## 1. Unix

Unix là một hệ điều hành từ đời Tống (1969) bắt đầu được một số nhân viên tại phòng Lab của [AT&T](https://vi.wikipedia.org/wiki/AT%26T){:target="_blank"} phát triển bao gồm [Ken Thompson](https://vi.wikipedia.org/wiki/Ken_Thompson){:target="_blank"} và [Dennis Ritchie](https://vi.wikipedia.org/wiki/Dennis_Ritchie){:target="_blank"}. 

<p align="center">
<img width="600" height="400" src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/46/Ken_Thompson_and_Dennis_Ritchie.jpg/225px-Ken_Thompson_and_Dennis_Ritchie.jpg" title="Nguồn whatis.techtarget.com">
</p>
<p align="center">
<span>Ảnh chụp hai thánh Thompson (trái) cùng với Dennis Ritchie.</span>
</p>


UNIX bắt đầu từ những năm giữa thập kỷ 60 của thế kỷ trước. Lúc đó Ken Thomson, Dennis Ritchie và những người khác nữa làm ra trên máy PDP-7 một thứ mà bây giờ gọi là UNIX. Chữ UNIX ban đầu viết là Unics là một kiểu chơi chữ của các tác giả khi so sánh sản phẩm của họ với hệ điều hành Multics lúc bấy giờ. Unics là chữ viết tắt của Uniplexed Information and Computing System. (_Nguồn wiki_)

Các phiên bản đầu tiên của Unix được viết bằng ngôn ngữ **[B](https://en.wikipedia.org/wiki/B_(programming_language)){:target="_blank"}**. Ngôn ngữ lập trình **B** này cũng được chính hai thánh này phát triển luôn.

Sau đó vào năm **1973**, lúc bấy giờ đã là phiên bản thứ 4 của Unix. Nhờ vào việc viết lại ngôn ngữ **B**, thánh Dennis Ritchie đã phát triển ngôn ngữ **[C](https://en.wikipedia.org/wiki/C_(programming_language)){:target="_blank"}** - ngôn ngữ lập trình phổ biến đến tận ngày nay đã giúp UNIX cải thiện tốc độ khi chuyển sang các phần cứng mới.

Những năm sau đó, do luật chống độc quyền của Mỹ đối với AT&T nên Unix đã được cung cấp miễn phí cho các trường đại học ở Bắc Mỹ từ năm **1975**.

Năm **1977** do được sửa mã nguồn nên trường đại học **Berkeley**, Califfornia đã phát triển một phiên bản Unix riêng cho mình được gọi là **BSD** (Berkeley Software Distribution). BSD phát triển từ version 1 đến version cuối cùng **4.4BSD-Lite Release 2** năm 1995.

Đó, dựa vào việc phát triển Unix và cũng cảm ơn luôn luật chống độc quyền của Mỹ mà ngày nay chúng ta có khá nhiều hệ điều hành cả mở cả đóng dựa trên ý tưởng của Unix. Có thể kể đến như **Mac OS** hay **Free BSD** đều là những phiên bản nổi trội nhất.

>_**Có thể bạn lại biết rồi**_: Hệ điều hành của PlayStation 4 là Orbis là một nhánh fork của FreeBSD verion 9.0.

## 2. Linux

Khái niệm đầu tiên khi nói đến lịch sử hình thành của Linux đó chính là **GNU**

[Richard Stallman](https://vi.wikipedia.org/wiki/Richard_Stallman){:target="_blank"} - một nhân viên kỹ thuật máy tính yêu thích và chuyên sử dụng phần mềm nguồn mở, tuy nhiên đến những năm của thập kỷ 80 thế kỷ trước hầu hết các phần mềm đều có bản quyền. Nhận thấy điều này có thể ngăn cản việc phát triển và kết nối giữa những nhà phát triển phần mềm. Richard Stallman đã khởi đầu dự án **GNU** vào năm 1983.


<p align="center">
<img width="600" height="500" src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/3d/Richard_Stallman_at_Pittsburgh_University.jpg/250px-Richard_Stallman_at_Pittsburgh_University.jpg" title="Nguồn whatis.techtarget.com">
</p>
<p align="center">
<span>Richard Stallman.</span>
</p>


Mục đích của **GNU** ban đầu là phát triển một hệ điều hành giống Unix nhưng phải được miễn phí và cộng đồng có thể tham gia sửa đổi, phát triển. GNU được cấu tạo từ các chữ đầu của cụm từ **"GNU's Not Unix"**

GNU đã tạo ra được rất nhiều sản phẩm quan trọng như **GNU Compiler Collection (gcc), GNU Debugger, GNU Emacs text editor (Emacs), GNU build automator (make)** … Ngoài ra còn phải kể đến giấy phép nổi tiếng được sử dụng rộng rãi nhất hiện nay: **GNU General Public License (GPL)**

GNU Project đã đạt được nhiều thành tựu lớn, tạo ra được nhiều công cụ tương tự như những gì có trên Unix. Tuy nhiên, GNU vẫn thiếu một thành phần quan trọng, mảnh ghép cuối cùng để nó trở thành một hệ điều hành hoàn chỉnh. Đó chính là **Kernel**, phần thực hiện công việc điều khiển, giao tiếp với các thiết bị phần cứng (CPU, RAM, Devices …).

Và [Linus Torvalds](https://vi.wikipedia.org/wiki/Linus_Torvalds){:target="_blank"} - một vị thánh tiếp theo xuất hiện trong bài viết này lộ diện.

<p align="center">
<img width="600" height="500" src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/5c/Linus_Torvalds_%28cropped%29.jpg/170px-Linus_Torvalds_%28cropped%29.jpg">
</p>
<p align="center">
<span>Linus Torvalds.</span>
</p>


Torvalds lần đầu tiên biết đến dự án GNU vào năm 1991, sau khi được bạn mình là Lars Wirzenius, đưa anh đến Đại học Công nghệ để nghe bài phát biểu của Richard Stallman. Torvalds sau đó đã sử dụng GNU General Public License phiên bản 2 (GPLv2) của Stallman để phát triển **kernel** của mình dựa vào **GNU C Compiler** (GNU C Compiler vẫn là lựa chọn chính để biên dịch Linux ngày nay.)

Vì sự kết hợp hoàn hảo này nên hãy thật công bằng khi gọi hệ điều hành của bạn là **GNU/Linux** nhé.

>_**Có thể bạn lại biết rồi**_: Bạn đã nghe đến phần mềm **git** rồi đúng không? Bạn có biết rằng, chính thánh Linus Torvalds là người phát triển git và sử dụng git để quản lý source code cho kernel project không ?. ([Github của thánh](https://github.com/torvalds){:target="_blank"})

## 3. GNU/Linux Distribution

Distribution hay còn gọi là Distro, là các bản phân phối, được phát triển dựa vào bản gốc, tuy nhiên cải thiện tính năng trong việc sử dụng cho phù hợp. Không có bản nào giống bản nào cả.

Do tính chất mở của cả Kernal lẫn GNU nên cộng đồng có thể tùy biến và phát triển các bản GNU/Linux cho riêng mình (các Distro)

Các bản Distro nổi tiếng nhất có thể kể đến như **Debian** (tiền thân của Distro Ubuntu) hay **RHEL** (Sau này phát triển thành **Fedora** và **CentOS**)

Ngoài ra còn có ti tỉ các bản Distro khác mà mình không thể liệt kê ra hết được. Các bạn có thể tham khảo thêm tại [đây](https://upload.wikimedia.org/wikipedia/commons/1/1b/Linux_Distribution_Timeline.svg){:target="_blank"} để tìm xem hệ điều hành của mình đang sử dụng có ông tổ là bản nào nhé :v

## 4. Linux kernel
Đây cũng chính là động lực mà mình muốn hướng tới trong bài viết này của mình =))

Tính đến thời điểm hiện tại Linux kernel đã [release](https://kernelnewbies.org/LinuxVersions){:target="_blank"} phiên bản stable của mình là **4.20**. Mình không giỏi đến mức tham gia phát triển Linux kernel, cũng chưa thể tối ưu Linux Kernel theo ý riêng của mình. Nhưng mình vẫn quan tâm đến Linux kernel vì nó có một tính năng đặc biệt quan trọng, là nền tảng để Docker phát triển sau này đó là **Cgroup - Linux Control Group**

>"if you have knowledge let others light their candles in it"

#### Tham khảo

[https://vi.wikipedia.org/wiki/GNU](https://vi.wikipedia.org/wiki/GNU){:target="_blank"}

[https://vi.wikipedia.org/wiki/Richard_Stallman](https://vi.wikipedia.org/wiki/Richard_Stallman){:target="_blank"}

[https://en.wikipedia.org/wiki/C_(programming_language)](https://en.wikipedia.org/wiki/C_(programming_language)){:target="_blank"}

[https://en.wikipedia.org/wiki/B_(programming_language)](https://en.wikipedia.org/wiki/C_(programming_language)){:target="_blank"}

[https://vi.wikipedia.org/wiki/AT%26T](https://vi.wikipedia.org/wiki/AT%26T){:target="_blank"}

[https://vi.wikipedia.org/wiki/Ken_Thompson](https://vi.wikipedia.org/wiki/Ken_Thompson){:target="_blank"}

[https://en.wikipedia.org/wiki/Linux](https://en.wikipedia.org/wiki/Linux){:target="_blank"}

[https://www.gnu.org/gnu/gnu-linux-faq.html](https://www.gnu.org/gnu/gnu-linux-faq.html){:target="_blank"}


---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>

