---
date: 2019-03-05
title: "OOM Killer - Anh Cảnh Sát Giao Thông trên Linux"
categories:
  - Linux
description: OOM Killer - Kẻ xử lý các process ngốn RAM trên Linux
author: ManhDV
tags: [Linux, CentOS7]
type: Document
---

## 1. Giới thiệu về OOM Killer

**OOM Killer** hoặc **Out Of Memory Killer** là một quá trình mà kernel linux sử dụng khi hệ thống đang trong tình trạng RAM bị quá tải. Như chúng ta đã biết, hệ thống là tập hợp gồm rất nhiều các process (tiến trình) chạy trên đó. Mỗi process đều sử dụng tài nguyên như RAM, CPU, Disk của hệ thống. Tuy nhiên tài nguyên của mỗi hệ thống đều có những giới hạn nhất định, và khi tài nguyên như RAM bị sử dụng tới mức báp động đỏ thì hệ thống phải có những động tác để ngăn chặn việc này.

Quy định của luật giao thông là mỗi xe máy chỉ được phép chở 2. Tiến trình nào kẹp 3, kẹp 4 sẽ lập tức có cách anh CSGT OOM tiến hành tuýt còi, bắt process đó tạm dừng và đưa lên đồn. 

## 2. Các process nào sẽ được chọn để bị kill

OOM Killer hoạt động bằng cách xem xét tất cả các quy trình đang chạy và gán cho chúng điểm số xấu. Quá trình có số điểm cao nhất là quá trình bị giết. OOM Killer chỉ định điểm xấu dựa trên một số tiêu chí. Nguyên tắc như sau:

	 - Quá trình và tất cả các tiến trình con của nó đang sử dụng rất nhiều RAM
	 - Số lượng process tối thiểu bị kill (lý tưởng là một) để giải phóng đủ RAM để giải quyết tình huống
	 - Root, kernel và các process hệ thống quan trọng được cho điểm thấp hơn nhiều

- Các tiến trình nào luôn bị giết?

Các tiêu chí được liệt kê ở trên có nghĩa là khi chọn một tiến trình để tiêu diệt OOM Killer sẽ chọn một quy trình sử dụng nhiều RAM và có nhiều tiến trình con và không phải là các tiến trình hệ thống. Một ứng dụng như apache, mysql, ftp server hoặc mail server sẽ tạo ra một ứng cử viên tốt.

Đối với các máy chủ chạy KVM, các tiến trình ưu tiên bị kill đó sẽ là các tiến trình chạy máy ảo. Và ở phần tiếp theo, mình sẽ dựng tình huống OOM Killer sẽ tuýt còi các process chạy máy ảo trên KVM.

## 3. Làm thế nào để biết có tiến trình bị OOM Killer xử lý?

Bất cứ khi nào OOM Killer được gọi để xử lý 1 tiến trình, nó sẽ ghi thông tin vào nhật ký hệ thống bao gồm tiến trình nào bị giết và tại sao. Chúng ta kiểm tra như sau :

```sh
dmesg | egrep -i "killed process"
```

Output sẽ như sau :

```sh
kernel kernel: Out of Memory: Kills process 2592 (mysql)
```

Trong trường hợp này, tiến trình đã bị kill là mysql có PID là 2592.

Đối với mỗi OS, thông tin về việc tiến trình bị kill bởi OOM sẽ xuất hiện tại các file khác 
## 4. Bài Lab OOM Killer với KVM

Chúng ta sẽ tìm hiểu OOM Killer sẽ xử lý các process chạy máy ảo sử dụng RAM quá tải như thế nào trong KVM.

Mô hình :

![log](/images/img-oom/oom-00.png)

Thực hiện test :

- Bước 1 : Trên máy KVM, kiểm tra các tiến trình đang chạy với lệnh `top`. Đánh dấu các tiến trình đang chạy máy ảo qemu-kvm

![log](/images/img-oom/oom-01.png)

- Bước 2 : Thực hiện cài stress trên 3 máy ảo

```sh
yum install epel-release -y
yum install stress -y
```

- Bước 3 : Thực hiện đẩy stress để RAM trên 3 máy ảo đạt mức tối đa

```sh
stress --vm 2 --vm-bytes 1G --timeout 300s
```

- Bước 4 : Sau 1 thời gian, OOM sẽ thực hiện việc kill tiến trình của máy ảo. Kiểm tra log như sau : 
```sh

Kết quả như sau : 

![oom](/images/img-oom/oom-03.png)

Đối chiếu lại tiến trình máy ảo đã chụp ở trên, chứng tỏ OOM Killer đã thực hiện việc kill 2 tiến trình máy ảo chạy nhiều RAM nhất.

## 5. Kiểm tra các VM bị kill do thiếu RAM trên OpenStack

Các hệ thống Cloud OpenStack có hypervisor là KVM, và các máy ảo chạy trên hypervisor đều được coi như là một tiến trình trong hệ thống. 

Khi máy ảo trên OpenStack bị Shutdown đột ngột chưa rõ nguyên nhân. Để kiểm tra xem máy ảo có phải bị kill bởi OOM do thiếu RAM hay không, ta cần thực hiện 3 bước sau : 

 - Bước 1 : Giám sát các tiến trình máy ảo đang chạy trong hệ thống. Sử dụng câu lệnh `top -c`. 

![oom](/images/img-oom/oom-04.png)

 - Bước 2 : Kiểm tra thông tin Log về việc OOM kill tiến trình. Kiểm tra ID của tiến trình bị kill có mapping với ID các tiến trình được giám sát ở trên hay không? Nếu có trùng, ta bước đầu xác minh tiến trình bị kill chính là tiến trình của máy ảo. 

 Tiếp tục kiểm tra lưu lượng sử dụng RAM của hypervisor trong khoảng thời gian tiến trình bị kill.
 
![oom](/images/img-oom/oom-05.png)

Có thể thấy Log về tiến trình qemu-kvm bị kill vào lúc 21 giờ 36 phút.

 - Bước 3 : Thực hiện kiểm tra RAM máy hypervisor tại thời điểm từ 21h - 22h :
 
![oom](/images/img-oom/oom-06.png)

Có thể thấy trong khoảng thời gian 21 giờ 36 phút, RAM tại hypervisor đã bị hết. Sau thời điểm tiến trình qemu-kvm chạy máy ảo bị OOM kill thì RAM available tại hypervisor đã trở lại ngưỡng ổn định. 

Từ những dữ liệu trên, có thể kết luận máy ảo sử dụng RAM nhiều nhất sẽ bị kill bởi OOM Killer nếu hypervisor bị quá tải về RAM.

### Xin cảm ơn đã theo dõi bài viết. Hẹn gặp lại vào chuỗi bài tiếp theo !
---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
