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

Xin chào mừng các bạn quay trở lại với lớp học về Log của mình ! 
Trước khi đi vào phần 2, chúng ta sẽ điểm lại một số nội dung chính có trong phần 1 nhé. Link tham khảo <a href="https://blog.cloud365.vn/logging/nhap-mon-logging-phan1/" target="_blank">phần 1</a>

**LOG là gì**

File log của hệ thống giống như những quyển sổ nhật ký, ghi lại toàn bộ quá trình hoạt động của hệ thống.

**LOG dùng để làm gì**

Những vai trò dễ nhận thấy của LOG là : 

 - TroubleShooting trong quá trình cài đặt các service.
 - Tra cứu nhanh các thông tin của hệ thống.
 - Truy vết các event đã và đang xảy ra.

Tại phần 2 này, mình sẽ giới thiệu các nội dung sau : 

 - Phân loại các file log cơ bản và quan trọng trong Linux.
 - Một số câu lệnh hay dùng để đọc và phân tích Log.
 
## 2. Phân loại file log trong Linux

Distro Linux mình hay sử dụng là Centos, vì vậy các nội dung trong chuỗi bài của mình sẽ hướng khá nhiều tới Centos. Những thông tin về LOG với các distro khác như Ubuntu mình sẽ cố gắng update vào một ngày đẹp trời sớm nhất nhé ^.^

Trên Centos, sau khi các bạn cài đặt một server Centos trắng tinh, thì sẽ file log mặc định có sẵn sẽ là : 

### 2.1. Message log : /var/log/messages
Chứa dữ liệu log của hầu hết các thông báo hệ thống nói chung, bao gồm cả các thông báo trong quá trình khởi động hệ thống.
Ví dụ như, khi bạn restart service network, sẽ có các log về networking xuất hiện. Sử dụng câu lệnh sau để xem các file log được realtime:

```sh
tail -f /var/log/messages
```

```sh
Nov  9 21:32:51 elk systemd: Stopping LSB: Bring up/down networking...
Nov  9 21:32:52 elk network: Shutting down interface ens160:  [  OK  ]
Nov  9 21:32:52 elk network: Shutting down interface ens192:  [  OK  ]
Nov  9 21:32:52 elk network: Shutting down loopback interface:  [  OK  ]
Nov  9 21:32:52 elk systemd: Starting LSB: Bring up/down networking...
Nov  9 21:32:53 elk network: Bringing up loopback interface:  [  OK  ]
Nov  9 21:32:53 elk kernel: vmxnet3 0000:03:00.0 ens160: intr type 3, mode 0, 3 vectors allocated
Nov  9 21:32:53 elk kernel: vmxnet3 0000:03:00.0 ens160: NIC Link is Up 10000 Mbps
Nov  9 21:32:57 elk network: Bringing up interface ens160:  [  OK  ]
Nov  9 21:32:57 elk kernel: vmxnet3 0000:0b:00.0 ens192: intr type 3, mode 0, 3 vectors allocated
Nov  9 21:32:57 elk kernel: vmxnet3 0000:0b:00.0 ens192: NIC Link is Up 10000 Mbps
Nov  9 21:33:01 elk network: Bringing up interface ens192:  [  OK  ]
Nov  9 21:33:01 elk systemd: Started LSB: Bring up/down networking.
```

File log message thể hiện rõ luồng xử lý của việc restart networking như sau : 

- **1**. Shutdown dịch vụ networking
 
```sh
Nov  9 21:32:51 elk systemd: Stopping LSB: Bring up/down networking...
```

- **2**. Các interface bị down xuống
 
```sh
Nov  9 21:32:52 elk network: Shutting down interface ens160:  [  OK  ]
Nov  9 21:32:52 elk network: Shutting down interface ens192:  [  OK  ]
Nov  9 21:32:52 elk network: Shutting down loopback interface:  [  OK  ]
Nov  9 21:32:52 elk systemd: Stopped LSB: Bring up/down networking.
```

- **3**. Dịch vụ network được bật lên

```sh
Nov  9 21:32:53 elk network: Bringing up loopback interface:  [  OK  ]
```

- **4**. Các interface được bật lên

```sh
Nov  9 21:32:53 elk network: Bringing up loopback interface:  [  OK  ]
Nov  9 21:32:53 elk kernel: vmxnet3 0000:03:00.0 ens160: intr type 3, mode 0, 3 vectors allocated
Nov  9 21:32:53 elk kernel: vmxnet3 0000:03:00.0 ens160: NIC Link is Up 10000 Mbps
Nov  9 21:32:57 elk network: Bringing up interface ens160:  [  OK  ]
Nov  9 21:32:57 elk kernel: vmxnet3 0000:0b:00.0 ens192: intr type 3, mode 0, 3 vectors allocated
Nov  9 21:32:57 elk kernel: vmxnet3 0000:0b:00.0 ens192: NIC Link is Up 10000 Mbps
Nov  9 21:33:01 elk network: Bringing up interface ens192:  [  OK  ]
```

- **5**. Dịch vụ network xác thực đã được bật
```sh
Nov  9 21:33:01 elk systemd: Started LSB: Bring up/down networking.
```

Những dòng log trên cho chúng ta 2 cách nhìn :
 - Nếu như có lỗi liên quan tới network, việc đầu tiên cần làm là xem log trong file message.
 - Nếu cứ có các dòng log trên xuất hiện, thì nghĩa là đã có event **Networking đã restart**.
 
### 2.2. Crontab log :  /var/log/cron 
File log cron chứa dữ liệu log của cron deamon. Bắt đầu và dừng cron cũng như cronjob thất bại.

**Giải thích nhanh** : Crob deamon là tiến trình giúp chúng ta thực hiện một hành động trên hệ thống một cách tự động theo mốc thời gian cố định.

Khi chúng ta thực hiện chạy một crontab thực hiện việc backup database với thời gian là 2h / lần.

Hãy cùng xem nội dung setup trong crontab với câu lệnh **crontab -e** :

```sh
0 */2 * * * /backup/backup_database.sh
```

Khi đó, trong file log `/var/log/cron` sẽ ghi lại quá trình crobtab được thực hiện. 
Sử dụng câu lệnh sau để lọc thông tin về việc chạy cron cho file `/backup/backup_database.sh`.

```sh
cat /var/log/cron  | grep /backup/backup_database.sh
```

```sh
Jan 24 00:00:01 controller1 CROND[34033]: (root) CMD (/backup/backup_database.sh)
Jan 24 02:00:01 controller1 CROND[24735]: (root) CMD (/backup/backup_database.sh)
Jan 24 04:00:01 controller1 CROND[14661]: (root) CMD (/backup/backup_database.sh)
Jan 24 06:00:01 controller1 CROND[3334]: (root) CMD (/backup/backup_database.sh)
Jan 24 08:00:01 controller1 CROND[57850]: (root) CMD (/backup/backup_database.sh)
Jan 24 10:00:01 controller1 CROND[46964]: (root) CMD (/backup/backup_database.sh)
Jan 24 12:00:01 controller1 CROND[37449]: (root) CMD (/backup/backup_database.sh)
Jan 24 14:00:01 controller1 CROND[28280]: (root) CMD (/backup/backup_database.sh)
Jan 24 16:00:01 controller1 CROND[19629]: (root) CMD (/backup/backup_database.sh)
Jan 24 18:00:01 controller1 CROND[10219]: (root) CMD (/backup/backup_database.sh)
Jan 24 20:00:01 controller1 CROND[723]: (root) CMD (/backup/backup_database.sh)
Jan 24 22:00:01 controller1 CROND[57462]: (root) CMD (/backup/backup_database.sh)
Jan 25 00:00:01 controller1 CROND[48920]: (root) CMD (/backup/backup_database.sh)
```

Chúng ta có thể thấy là việc chạy file `/backup/backup_database.sh` được diễn ra đều đặn 2 tiếng 1 lần.

### 2.3. Log về Login - Logout  : /var/log/wtmp
 
File log `wtmp` chứa tất cả các đăng nhập và đăng xuất lịch sử.

Tuy nhiên, file log `wtmp` có định dạng khá đặc biệt. Để đọc được file log này, chúng ta sử dụng câu lệnh sau

```sh
utmpdump /var/log/wtmp | less
```

```sh
Utmp dump of /var/log/wtmp
[8] [20302] [    ] [        ] [pts/0       ] [                    ] [0.0.0.0        ] [Sat Nov 10 20:21:59 2018 +07]
[7] [20500] [ts/0] [root    ] [pts/0       ] [27.72.59.xxx        ] [27.72.59.xxx   ] [Sat Nov 10 20:22:14 2018 +07]
[8] [20441] [    ] [        ] [pts/1       ] [                    ] [0.0.0.0        ] [Sat Nov 10 20:22:20 2018 +07]
```

Mỗi khi có người dùng logout khỏi hệ thống, định dạng log sẽ như sau :

```sh
[8] [20302] [    ] [        ] [pts/0       ] [                    ] [0.0.0.0        ] [Sat Nov 10 20:21:59 2018 +07]
```

Còn đây là định dạng log mỗi khi có người login vào hệ thống : 

```sh
[7] [20500] [ts/0] [root    ] [pts/0       ] [27.72.59.xxx        ] [27.72.59.xxx   ] [Sat Nov 10 20:22:14 2018 +07]
```

Trong đó, IP `27.72.59.xxx` là ip của người dùng đang login vào hệ thống.

### 2.4. Log phần cứng : /var/log/dmesg
 
Chứa thông tin bộ đệm kernel ring. Khi hệ thống khởi động, file log sẽ chứa thông tin về các thiết bị phần cứng mà kernel phát hiện được. Các message này có sẵn trong kernel ring buffer và bất cứ khi nào có message mới xuất hiện, message sẽ bị ghi đè. Bạn cũng có thể xem nội dung của tệp này bằng lệnh dmesg.

Chúng ta hãy thử xem 10 dòng log cuối cùng trong file log dmesg mỗi khi một máy ảo trên hệ thống Cloud Nhân Hòa được tạo :

```sh
tail -n 10 /var/log/dmesg
```

```sh
[    6.542930] systemd[1]: Inserted module 'ip_tables'
[    3.358422]  vda: vda1
[    7.014517] EXT4-fs (vda1): re-mounted. Opts: (null)
[    7.107034] systemd-journald[1355]: Received request to flush runtime journal from PID 1
[    2.449704] systemd[1]: Starting Journal Service...
[    2.455997] systemd[1]: Starting Create list of required static device nodes for the current kernel...
[    2.465960] systemd[1]: Starting Setup Virtual Console...
[    2.472855] systemd[1]: Starting Apply Kernel Variables...
[    7.583101] piix4_smbus 0000:00:01.3: SMBus Host Controller at 0x700, revision 0
[    7.773026] input: PC Speaker as /devices/platform/pcspkr/input/input5
```

Chúng ta hãy tập trung vào một số file log liên quan tới phần cứng như : 

```sh
[    3.358422]  vda: vda1
[    7.014517] EXT4-fs (vda1): re-mounted. Opts: (null)

[    7.583101] piix4_smbus 0000:00:01.3: SMBus Host Controller at 0x700, revision 0
```

2 log trên cho chúng ta thấy thông tin về ổ cứng và SMBus của máy ảo được in ra khi máy ảo được khởi tạo. 

### 2.5 Log về SSH : /var/log/secure

Tiếp theo, chúng ta hãy cùng phân tích một file log rất quan trọng và mang lại nhiều thông tin hữu ích. Đó chính là file log `secure` chứa các nội dung về việc SSH tới hệ thống.

Việc SSH tới hệ thống có thể chia thành 3 trường hợp chính :

- Login SSH thành công.

Mỗi khi user SSH thành công vào hệ thống, các dòng log sau sẽ xuất hiện tại `/var/log/secure`.

```sh
Jan 18 15:39:30 web sshd[14838]: Accepted password for duydm from 27.72.59.xxx port 49572 ssh2
Jan 18 15:39:30 web sshd: Started Session 3057 of user duydm
```

Các bạn thấy user **duydm** có chút quen thuộc bài không ạ?.

Hãy dành 30s để đọc lại <a href="https://blog.cloud365.vn/logging/nhap-mon-logging-phan1/" target="_blank">Tính huống 2</a> của bài trước nhé. Chắc hẳn mọi người vẫn nhớ cách mà Thành đã thoát khỏi một bàn thua ngoạn mục nhờ việc phân tích log SSH chứ !

Từ log SSH thành công, ta có thể phân tích thành các dữ liệu sau :

	+ User login SSH (duydm)
	+ IP login SSH (27.72.59.xxx)
	+ Source Port của tiến trình SSH (49572)
	+ Kết quả việc SSH (Accepted password)
	
- Login SSH thất bại
 
Vậy thì việc SSH bị thất bại, thì trong file log sẽ có gì?

```sh
Nov 10 20:54:32 elk sshd[26205]: Failed password for root from 218.92.1.148 port 21450 ssh2
Nov 10 20:54:32 elk sshd[26205]: Received disconnect from 218.92.1.148 port 21450:11:  [preauth]
Nov 10 20:54:32 elk sshd[26205]: Disconnected from 218.92.1.148 port 21450 [preauth]
```

Về cơ bản, các thông tin được sàng lọc ra vẫn giống với khi có login SSH thành công. Tuy nhiên, kết quả việc SSH sẽ khác :

  - Kết quả việc SSH : Failed password
	 
- Logout SSH

Và cuối cùng, mỗi khi user login phiên SSH thì các file log sẽ có các thông tin sau : 

```sh
Nov 10 21:06:46 elk sshd[28506]: Received disconnect from 27.72.59.xxx port 56671:11: disconnected by user
Nov 10 21:06:46 elk sshd[28506]: Disconnected from 27.72.59.xxx port 56671
Nov 10 21:06:46 elk sshd[28506]: pam_unix(sshd:session): session closed for user root
Nov 10 21:06:47 elk sshd[28511]: Connection closed by 27.72.59.xxx port 56673 [preauth]
```

## 3. Tổng kết các câu lệnh đọc log hay dùng.

Từ các ví dụ trong phần 2, chúng ta có thể tổng kết lại các câu lệnh hay được dùng trong việc đọc và phân tích log như sau :

- Xem file log realtime với **tail -f**

```sh
tail -f /var/log/messages
```

- Lọc thông tin với **grep**
 
```sh
cat /var/log/cron  | grep /backup/backup_database.sh
```

- Đọc định dạng file `wtmp` với **utmpdump**, và sử dụng **less** để hiện thị một phần của file.

```sh
utmpdump /var/log/wtmp | less
```

- In ra màn hình 10 dòng log cuối cùng với **tail -n 10** . Thay 10 với số dòng bạn muốn in ra. 
 
```sh
tail -n 10 /var/log/dmesg
```

- Có ai thấy user **duydm** này đáng nghi không ạ? Hãy cùng in ra tất cả các file log có chứa cụm từ duydm trong thư mục `/var/log/` với câu lệnh :

```sh
grep -Rin "duydm" /var/log/
```

## 4. Chuyên mục tổng kết và rút kinh nghiệm 

Qua các bài trên, hi vọng mọi người có thể nắm được các kiến thức sau : 

- Phân loại và làm rõ chức năng của từng loại file log mặc định trong Linux
- Thực hành được các câu lệnh đọc và phân tích log.
 
## 5. Ở bài tiếp theo sẽ có gì?

Ở bài tiếp theo, chúng ta sẽ tìm hiểu 2 vấn đề chuyên sâu hơn một chút nhé :

- Linux xử lý các file Log như thế nào?
- Các giao thức thường được áp dụng trong việc xử lý Log.
 
Cảm ơn và hẹn gặp mọi người tại bài tiếp theo ^.^ 