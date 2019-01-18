---
date: 2019-01-18
title: Series bí kíp support dạo Để trở thành cao thủ LOGGING
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

Trong loạt những bài viết này, mình sẽ chia sẻ những kiến thức rất cơ bản, nhưng mình thấy râts hữu ích và hỗ trợ mang lại hiệu quả cao trong quá trình làm hệ thống, đó chính là LOG trong linux. 

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

Trong Linux, các file log thường được đặt tại một thư mục **/var/log/** . Các log của dịch vụ thường được đặt tại các thư mục con bên trong như **/var/log/httpd/**

Sau khi áp dụng các theo dõi log mỗi khi cài đặt dịch vụ, Thành đã dựng được LAB thành công. Thành phục quá, liền khăn gói quả bí tới Nhân Hòa tầm sư học Cloud. 

#### 2. Bài học 2 : 

Sau 6 tháng Thành cày cuốc học Cloud tại Nhân Hòa, đã được sếp tin tưởng giao cho triển khai một hệ thống Cloud khá lớn cho khách hàng. Thành làm rất hăng, ngày đêm dựng hệ thống. Sau 9,6 ngày đêm không ngủ, hệ thống Cloud Thành dựng cho khách hàng đã running. Cấp trên vui mừng, đồng nghiệp nể phục, khen Thành khá Bá.

Thế nhưng, không hiểu sao cứ tạo máy ảo được 2 3 hôm thì máy ảo lại không cánh mà bay.
Mọi người bắt đầu nghi ngờ hệ thống Cloud của Thành build ra ...lởm. Khách hàng trách móc, cấp trên ngờ vực. Thành vô cùng suy sụp. 

Thấy tội nghiệp, anh Đạt 09 đồng nghiệp liền giúp đỡ Thành. Dạy Thành cách theo dấu vụ việc như sau : 

 - Xác định cấu hình của dòng log mỗi khi có máy ảo bị xóa.
 - Tìm các log xóa máy ảo, sau đó xác định thời gian xóa máy ảo.
 - Tìm các log ghi lại các tiến trình chạy lặp đi lặp lại trong crontab. 

![log](/images/img-logging/log-01.png)

 - Đầu tiên, Thành đọc thông tin về việc tạo, xóa máy ảo sẽ được lưu tại file log : `/var/log/nova/nova-compute.log` với câu lệnh **cat**. 
 - Sau đó, sử dụng lệnh **grep** để lọc ra tất cả các dòng log chứa keyword **destroyed**, keyword này xuất hiện mỗi khi có máy ảo bị xóa. 

Chúng ta sẽ cùng Thành phân tích file log bắt được : 

```sh
2018-10-08 15:53:12.706 5200 INFO nova.virt.libvirt.driver [req-04018b09-55ef-426b-acda-a5ef3d7784d9 6cdd7041895a4ce8a50636232e2ea62d c540d81b72bd4c119d5dd3e0a59a4ebd - default default] [instance: 3a505027-d426-43d0-9fe9-0086fcdad7b4] Instance destroyed successfully.
```

Ta có thể tạm chia dòng log trên thành 2 phần khác nhau :

##### Phần 1 : Thời gian của sự kiện
```sh
2018-10-08 15:53:12.706
```

Phân tích nhỏ hơn cú pháp của phần này sẽ là : 

```sh
Year-Month-Day Hour-Minute-Second.Millisecond
```

Thành lập lại một bảng thống kê theo thời gian để xem có sự việc có xảy ra theo quy luật hay không?

##### Phần 2 : Nội dung sự kiện
 
```sh
5200 INFO nova.virt.libvirt.driver [req-04018b09-55ef-426b-acda-a5ef3d7784d9 6cdd7041895a4ce8a50636232e2ea62d c540d81b72bd4c119d5dd3e0a59a4ebd - default default] [instance: 3a505027-d426-43d0-9fe9-0086fcdad7b4] Instance destroyed successfully.
```

Chúng ta tạm bỏ qua các phần khá phức tạp ở phía đầu. Phần nội dung quan trọng cần chú ý chính là `Instance destroyed successfully`. Thông tin trên chứng tỏ việc máy ảo đã bị xóa!

Sau một tuần thu thập và phân tích log, cuối cùng Thành cũng đã nắm được quy luật. Cứ đến 2h sáng thứ 3,5,7 thì máy ảo bị xóa, và trước đó đều đặn lúc 1h55p thì crontap chạy file `test.sh`.

Thành boàng hoàng nhận ra nội dung file chính là các câu lệnh xóa máy ảo, chạy dưới quyền xác thực của user DuyDM, user dành cho nhân viên Đặng Mạnh Duy. Thủ phạm nhanh chóng thủ nhận, vì ghen ghét độ Bá của Thành nên tìm cách hãm hại. 

Thủ phạm bị trừng trị, Thành lại được đồng nghiệp công nhận là Thành Bá như ngày nào.  

#### 3. Chuyên mục tổng kết và rút kinh nghiệm 

- Các cao thủ khi xử lý những lỗi như ở **ví dụ số 1** , thay vì ngồi dò lại từng dòng cấu hình, xem lại từng câu lệnh. Họ sẽ dùng cách xử lý nhanh hơn đó chính là xác định xem nguyên nhân của lỗi xảy ra bằng cách đọc Log.

- Và ở **ví dụ số 2**, những người có nhiều kinh nghiệm sẽ xác định được nhanh chóng cách thức tra vết và phân tích một sự việc xảy ra trong hệ thống từ những thông tin mà Log cung cấp.


Qua 2 ví dụ trên. Chúng ta có thể thấy 1 số vai trò thiết thực của Log có thể thấy và áp dụng đó là : 

 - TroubleShooting trong quá trình cài đặt các service.
 - Tra cứu nhanh các thông tin của hệ thống.
 - Truy vết các event đã và đang xảy ra.

#### 4. Ở bài tiếp theo sẽ có gì?

Trên đây chỉ là một số những công dụng cơ bản nhất của Log trong Linux. Ở những bài tiếp theo, mình sẽ giới thiệu một số thông tin như :
 
 - Phân loại các file log cơ bản và quan trọng
 - Một số câu lệnh hay dùng để đọc và phân tích Log.
 - Linux xử lý các file Log như thế nào?
 - Các giao thức thường được áp dụng trong việc xử lý Log.
 
Xin chào và hẹn gặp lại ở bài tiếp theo !
 