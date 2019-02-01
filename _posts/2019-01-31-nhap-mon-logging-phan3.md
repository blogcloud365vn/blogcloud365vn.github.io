---
date: 2019-01-23
title: "Để trở thành cao thủ LOGGING - Phần 2"
categories:
  - Logging
description: Tuyển tập LOGGING từ mầm non tới đại học
author: manhdv
tags: [Beginer, Linux, Logging]
type: Document
---

## 1. Chuyên mục điểm tin vắn

```sh
Mỗi năm hoa đào nở
Lại thấy ông Sys già
Bày thịt gà xôi gấc
Khấn cụ (server) : " Đừng tạch nha"
```

Trước tiên xin chúc toàn thể các bạn đọc của Blog 365 có một cái Tết ấm no, hạnh phúc và đặc biệt AN TOÀN (server hệ thống dù Tết Tây hay Ta đều uptime 100%). 

Như thường lệ, hãy cùng điểm lại một số nội dung có trong <a href="https://blog.cloud365.vn/logging/nhap-mon-logging-phan2/" target="_blank">phần 2</a> của chuỗi series về LOG. 

Những tiêu điểm cần nắm vững trong bài thứ 2 đó là : 

- Phân loại các file log mặc định của Linux
- Chức năng của từng file Log
- Các câu lệnh đọc và phân tích Log

Và tại phần 3 này, mình sẽ giới thiệu giao thức Log cổ xưa nhất, lâu đời nhất, nhưng vẫn vô cùng hiệu quả và được sử dụng tới tận bây giờ. Đó chính là **Syslog**. 

## 2. Giới thiệu về Syslog trong Linux

Như chúng ta đã tìm hiểu trong phần 2, khi một máy Linux được cài lên, máy đó sẽ tự động sinh ra các file log mặc định. Và người quản lý việc sinh ra log đó chính là **Syslog**.

### Vậy Syslog là gì?

Syslog là một **giao thức** dùng để xử lý các file log Linux. Các file log có thể được lưu tại chính máy Linux đó, hoặc có thể di chuyển và lưu tại 1 máy khác. (Lý do phải chuyển các log lưu tại nơi khác bạn hãy xem ở phần 4 nhé !).

Máy nhận log thường được gọi là syslogd, syslog daemon hoặc syslog server. Syslog có thể gửi qua UDP hoặc TCP. Các dữ liệu được gửi dạng cleartext. Syslog dùng cổng 514.

### Syslog được phát triển như thế nào?

Syslog được phát triển năm 1980 bởi Eric Allman, nó là một phần của dự án Sendmail, và ban đầu chỉ được sử dụng duy nhất cho Sendmail. Nó đã thể hiện giá trị của mình và các ứng dụng khác cũng bắt đầu sử dụng nó. Syslog hiện nay trở thành giải pháp khai thác log tiêu chuẩn trên Unix-Linux cũng như trên hàng loạt các hệ điều hành khác và thường được tìm thấy trong các thiết bị mạng như router Trong năm 2009, Internet Engineering Task Forec (IETF) đưa ra chuẩn syslog trong RFC 5424.

Syslog ban đầu sử dụng UDP, điều này là không đảm bảo cho việc truyền tin. Tuy nhiên sau đó IETF đã ban hành RFC 3195 (Đảm bảo tin cậy cho syslog) và RFC 6587 (Truyền tải thông báo syslog qua TCP). Điều này có nghĩa là ngoài UDP thì giờ đây syslog cũng đã sử dụng TCP để đảm bảo an toàn cho quá trình truyền tin.

### Syslog được dùng như thế nào?

Syslog là một giao thức, và được sử dụng bới dịch vụ **Rsyslog**. Dịch vụ Rsyslog mới là người đưa ra các quyết định như sử dụng port nào để vận chuyển log, sau bao nhiêu lâu thì log sẽ được xoay vòng... 

Chúng ta cần làm rõ các khái niệm xoay quanh Syslog :

- Syslog : Giao thức dùng để xử lý file log trong Linux.
- Rsyslog : Dịch vụ sử dụng Syslog


## 3. Các từ khóa quan trọng 

Trong quá trình làm việc với Syslog, chúng ta cần chú ý tới các keyword sau

### Facility Level

Facility code được dùng để chỉ ra chương trình được ghi log lại. 

|Facility Number|Nguồn tạo log | Ý nghĩa |
|---------|--------------|---------|
|0|kernel | Những log mà do kernel sinh ra |
|1|user | Log ghi lại cấp độ người dùng|
|2|mail | Log của hệ thống mail |
|3|daemon | Log của các tiến trình trên hệ thống |
|4|auth | Log từ quá trình đăng nhập hệ hoặc xác thực hệ thống |
|5|syslog | Log từ chương trình syslogd |
|6|lpr | Log từ quá trình in ấn |
|7|news | Thông tin từ hệ thống | 
|8|uucp | Log UUCP subsystem |
|9|cron|Clock deamon|
|10|authpriv|Quá trình đăng nhập hoặc xác thực hệ thống|
|11|ftp|Log của FTP deamon|
|12|ntp|Log từ dịch vụ NTP của các subserver|
|13|security|Kiểm tra đăng nhập|
|14|console|Log cảnh báo hệ thống|
|15|solaris-cron|Log từ clock daemon|
|16 - 23|local 0 -local 7|Log dự trữ cho sử dụng nội bộ|

### Severity Level

Mỗi dòng log do Syslog sinh ra đều được gắn tag, thông báo về độ quan trọng của file log đó. Thống kê số lượng về mức độ cảnh báo của các file log cũng cho chúng ta thấy một phần tình trạng hệ thống. Nếu số lượng log **WARN** và **ERROR** xuất hiện nhiều thì chứng tỏ hệ thống của bạn không ổn một chút nào rồi !

|Code|Mức cảnh báo|	Ý nghĩa|
|---------|--------------|---------|
|0|emerg|	Thông báo tình trạng khẩn cấp|
|1|alert|	Hệ thống cần can thiệp ngay|
|2|crit|	Tình trạng nguy kịch|
|3|error|	Thông báo lỗi đối với hệ thống|
|4|warn|	Mức cảnh báo đối với hệ thống|
|5|notice|	Chú ý đối với hệ thống|
|6|info|	Thông tin của hệ thống|
|7|debug|	Quá trình kiểm tra hệ thống|

## 4. Mô hình Log tập trung

### Phương pháp **Quản lý tập trung**

Chắc hẳn sau 2 bài đầu tiên của chuỗi series, bạn đọc đã có thể thấy được tác dụng to lớn của việc đọc và phân tích log như thế nào rồi đúng không ạ?

Việc đọc Log một cách thủ công bằng câu lệnh như **tail, cat, grep...** chỉ áp dụng được hiệu quả khi số lượng các server cần được xử lý ít và việc đọc log không diễn ra thường xuyên.

Khi số lượng server cần được xử lý tăng lên nhiều hơn thì sao? 

Một vị tướng quân tài ba, quản lý hàng nghìn, hàng vạn binh lính, sẽ không cần hỏi từng anh lính một để biết được tình trạng đội quân của mình. Cách nhanh và đơn giản nhất đó chính là tổng hợp thông tin từ báo cáo của các anh đội trưởng. Đó chính là phương pháp **QUẢN LÝ TẬP TRUNG**.

Phương pháp **Quản lý tập trung** với log được Syslog có thể hiểu một cách đơn giản : Log tâp trung là quá trình tập trung, thu thập, phân tích... các log cần thiết từ nhiều nguồn khác nhau về một nơi an toàn để thuận lợi cho việc phân tích, theo dõi hệ thống.

### Tại sao lại phải sử dụng Log tập trung?

- Do có nhiều nguồn sinh log
	 - Có nhiều nguồn sinh ra log, log nằm trên nhiều máy chủ khác nhau nên khó quản lý.
	 - Nội dung log không đồng nhất (Giả sử log từ nguồn 1 có có ghi thông tin về ip mà không ghi thông tin về user name đăng nhập mà log từ nguồn 2 lại có) -> khó khăn trong việc kết hợp các log với nhau để xử lý vấn đề gặp phải.
	 - Định dạng log cũng không đồng nhất -> khó khăn trong việc chuẩn hóa
	 
- Đảm bảo tính toàn vẹn, bí mật, sẵn sàng của log.
	 - Do có nhiều các rootkit được thiết kế để xóa bỏ logs.
	 - Do log mới được ghi đè lên log cũ -> Log phải được lưu trữ ở một nơi an toàn và phải có kênh truyền đủ đảm bảo tính an toàn và sẵn sàng sử dụng để phân tích hệ thống.

### Lợi ích của Log tập trung

- Giúp quản trị viên có cái nhìn chi tiết về hệ thống
- Các file log được sao lưu và backup an toàn và lâu dài tại Syslog server
- Log tập trung kết hợp với các ứng dụng thu thập và phân tích log khác nữa giúp cho việc phân tích log trở nên thuận lợi hơn. 

## 5. Cấu hình Log tập trung với Syslog

Phương pháp **Quản lý tập trung** với log được Syslog áp dụng như sau :

- Tại máy cần đẩy log (**Syslog Client**) và máy nhận log (**Syslog Server**) thiết lập mối quan hệ.
- Tại Syslog Client setup các file log cần gửi.
- Tại Syslog Server tạo các thư mục riêng cho từng Client.
- Log của client được gửi tới và lưu tại thư mục riêng.

## 4. Chuyên mục tổng kết và rút kinh nghiệm 

## 5. Ở bài tiếp theo sẽ có gì?


---
Thực hiện bởi [cloud365.vn](https://cloud365.vn/)
