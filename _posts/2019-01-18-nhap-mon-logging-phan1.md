---
date: 2019-01-18
title: "Để trở thành cao thủ LOGGING - Part 1"
categories:
  - Logging
description: Tuyển tập LOGGING từ mầm non tới đại học
author: manhdv
tags: [Beginer, Linux, Logging]
type: Document
---

### 1. Lời dạo đầu

Đã bao nhiêu lần, các bạn sinh viên thực tập đã gặp phải trố mắt khi nhìn những bug nhỏ xinh, khiến mình khổ sở, đã được các Senior xử lý nuột nà chỉ trong 5p...

Các senior có bí quyết gì, giúp họ debug nhanh hơn bạn gấp mấy chục lần vậy? Họ biết nhiều thuật toán cao cấp? Họ am hiểu được hệ thống cực khủng? Họ đọc code siêu nhanh chăng?

Vậy thì nhầm to ! Các cao thủ chỉ hơn bạn về sự vững chắc trong nền tảng kiến thức và kinh nghiệm làm việc, những trải nghiệm về tình huống mà họ đã gặp trong thực tế. Và đi cùng với đó là những công cụ họ sử dụng, quan trọng hơn, đó là cách họ áp dụng. 

Trong loạt những bài viết này, mình sẽ chia sẻ những kiến thức rất cơ bản, nhưng mình thấy rất hữu ích và hỗ trợ mang lại hiệu quả cao trong quá trình làm hệ thống, đó chính là LOG trong linux. 

Chuỗi bài trong phần này sẽ là những kiến thức từ cơ bản tới chi tiết và nâng cao về Log trong Linux. Đi kèm với đó là những tình huống thực tế hay gặp phải và những bài LAB để các bạn có thể tự mình kiểm chứng.
 
#### Ok ! Let's go to the LOG ! 

## I. Nhập môn Logging. 

### 1. Log là gì? Tại sao phải đọc Log làm gì ???

#### 1. Bài học 1 : 

Thành là một sinh viên gương mẫu, chăm chỉ và thích các công nghệ mới, đặc biệt là Cloud. Sau một thời gian dùi mài kinh sử, Thành quyết tâm làm đồ án về Cloud OpenStack để mong đạt tấm bằng giỏi mơ ước. Thành vùi đầu vào nghiên cứu, đọc docs, thuê cả VPS Cloud tại trang https://cloud365.vn để dựng LAB. Sau 7x7 49 lần cài đặt thất bại, Thành liền pm tới một cao thủ Cloud trong cộng đồng là Gia Cát Tướng Công, hiện đang nắm quân tại Nhân Hòa. 

Gia Cát Tướng Công liền bày cho Thành 3 bước fix bug khi cài đặt dịch vụ : 

 - Xác định các dịch vụ được cài đặt sẽ bắn ra những file log nào? Và những file log đó được đặt ở đâu?
 - Trong quá trình cài đặt dịch vụ, bật ngay một tab nữa để xem log cài đặt.
 - Khi cài đặt xong, lại bật thêm một tab nữa để xem log start, stop của dịch vụ.
 - Khi test dịch vụ. Lại soi thêm log lần nữa để xem có lỗi hay không?
	
![log](/images/img-logging/log-00.png)

Những file log của hệ thống giống như những quyển sổ nhật ký, ghi lại toàn bộ quá trình hoạt động của hệ thống. Mỗi file log đều có những công dụng riêng. Có những log chuyên để ghi lại các sự kiện về user đăng nhập SSH, có file log chuyên ghi lại các gói đã được cài đặt... Đặc biệt, các service được cài đặt thường có những thư mục chứa những file log riêng của chúng. 

Trong Linux, các file log thường được đặt tại một thư mục ``/var/log/`` . Các log của dịch vụ thường được đặt tại các thư mục con bên trong như``/var/log/httpd/``. 

Sau khi áp dụng các theo dõi log mỗi khi cài đặt dịch vụ, Thành đã dựng được LAB thành công. Thành phục quá, liền khăn gói quả bí tới Nhân Hòa tầm sư học Cloud. 

#### 2. Bài học 2 : 

Sau khi vào Nhân Hòa được 1 tháng, Thành được giao cho nhiệm vụ quản lý một máy Web wordpress trên máy ảo VPS trên hệ thống Cloud Nhân Hòa. 

Thành liền cài đặt phần mềm giám sát mà Nhân Hòa hay sử dụng lên máy VPS để theo dõi. Qua 1 tuần giám sát, máy ảo VPS chạy rất mượt mà, các chỉ số rất ổn định. Sếp rất hài lòng về chất lượng giám sát của Thành. 

Vậy mà chỉ sau 1 đêm, toàn bộ file cấu hình web của máy VPS của Thành bị xóa sạch. Khách hàng phản ứng rất gay gắt, cấp trên trách móc. Thành vô cùng suy sụp. 

Thấy tội nghiệp, anh Đạt 09 đồng nghiệp liền giúp đỡ Thành. Dạy Thành cách theo dấu vụ việc như sau : 

 - Theo dõi file log chứa thông tin về user SSH vào hệ thống.
 - Tìm kiếm file log ghi lại các command được thực hiện trong hệ thống. 
 
Chúng ta sẽ cùng Thành phân tích cách file log thu thập được như sau : 

- File 2 : 

Với file 1, chúng ta có thể chú ý tới các log sau : 
```sh
Jan 18 15:39:30 web sshd[14838]: Accepted password for duydm from 27.72.59.135 port 49572 ssh2

Jan 18 15:39:34 web su: (to root) duydm on pts/1
```

- File 1 : Log chứa thông tin thực hiện command.

![log](/images/img-logging/log-02.png)

Với file 1, chúng ta có thể chú ý tới các log sau : 

```sh
Jan 18 15:40:07 web bash: root [14870]: 18/01/19 15:40:07 rm -rf /var/log/httpd/ [0]export
Jan 18 15:40:13 web bash: root [14870]: 18/01/19 15:40:13 rm -rf /etc/httpd/ [0]export
```


Chúng ta có thể thấy, tuy các file ghi lại các sự kiện khác nhau, tuy nhiên format của chúng vẫn giống nhau. 

Ta có thể tạm chia dòng log trên thành 2 phần khác nhau :

##### Phần 1 : Thời gian của sự kiện
```sh
Jan 18 15:40:07
```

Phân tích nhỏ hơn cú pháp của phần này sẽ là : 

```sh
Year-Month-Day Hour-Minute-Second.Millisecond
```

##### Phần 2 : Nội dung sự kiện
 
```sh
web sshd[14838]: Accepted password for duydm from 27.72.59.135 port 49572 

web su: (to root) duydm on pts/1

web bash: root [14870]: 18/01/19 15:40:07 rm -rf /var/log/httpd/ [0]export

web bash: root [14870]: 18/01/19 15:40:13 rm -rf /etc/httpd/ [0]export

```

Chúng ta tạm bỏ qua các phần khá phức tạp ở phía đầu. Phần nội dung quan trọng cần chú ý chính là :
 
 - Dòng 1 : ``Accepted password for duydm from 27.72.59.135 port 49572 `` , user **duydm** đã đăng nhập SSH từ IP **27.72.59.135**.
 - Dòng 2 : ``su: (to root) duydm`` , user **duydm** thực hiện quyền root với **su**
 - Dòng 3 : ``rm -rf /var/log/httpd/`` , câu lệnh xóa thư mục `/var/log/httpd/`
 - Dòng 4 : ``rm -rf /etc/httpd/``, câu lệnh xóa thư mục `/etc/httpd/`

Có thể tóm tắt lại sự việc như nhau : 

 - User **duydm** log vào VPS Web Wordpress
 - User **duydm** thực hiện chuyển đổi sang user root
 - Câu lệnh xóa file ``/var/log/httpd/`` được thực hiện.
 - Câu lệnh xóa file ``/etc/httpd/`` được thực hiện. 
 
Thủ phạm là nhân viên Đặng Mạnh Duy nhanh chóng thừa nhận hành vi phạm tội. Thành được minh oan và được cấp trên khen ngợi vì cách xử lý vấn đề rất tốt. Hôm sau đi liên hoan, Thành cảm ơn ngay anh Đạt 2 chục chén. 

#### 3. Chuyên mục tổng kết và rút kinh nghiệm 

- Các cao thủ khi xử lý những lỗi như ở **ví dụ số 1** , thay vì ngồi dò lại từng dòng cấu hình, xem lại từng câu lệnh. Họ sẽ dùng cách xử lý nhanh hơn đó chính là xác định xem nguyên nhân của lỗi xảy ra bằng cách đọc Log.

- Và ở **ví dụ số 2**, những người có nhiều kinh nghiệm sẽ xác định được nhanh chóng cách thức tra vết và phân tích một sự việc xảy ra trong hệ thống từ những thông tin mà Log cung cấp.


Qua 2 ví dụ trên. Chúng ta có thể thấy 1 số vai trò thiết thực của Log có thể thấy và áp dụng đó là : 

 - TroubleShooting trong quá trình cài đặt các service.
 - Tra cứu nhanh các thông tin của hệ thống.
 - Truy vết các event đã và đang xảy ra.

``Bật mí nhỏ `` : File log `cmd.log` mà Thành tra cứu không tự sinh ra đâu các bạn nhé. Muốn biết bí quyết để tạo ra log như vậy, xin mời đọc các bài sau :D 

#### 4. Ở bài tiếp theo sẽ có gì?

Trên đây chỉ là một số những công dụng cơ bản nhất của Log trong Linux. Ở những bài tiếp theo, mình sẽ giới thiệu một số thông tin như :
 
 - Phân loại các file log cơ bản và quan trọng
 - Một số câu lệnh hay dùng để đọc và phân tích Log.
 - Linux xử lý các file Log như thế nào?
 - Các giao thức thường được áp dụng trong việc xử lý Log.
 
Xin chào và hẹn gặp lại ở bài tiếp theo !
 