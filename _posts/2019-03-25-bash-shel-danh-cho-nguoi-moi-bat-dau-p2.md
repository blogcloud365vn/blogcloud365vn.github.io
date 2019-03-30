---
date: 2019-03-25
title: "Bash Shell dành cho người mới bắt đầu - Chương 2"
categories:
  - Shell
description: Bash Shell dành cho người mới bắt đầu
author: manhdv
tags: [Beginer, Linux, Shell]
type: Document
---


### 1 : Sử dụng Rediection trong Shell

Kỹ thuật redirection là kỹ thuật dùng toán tử `>`. redirect từ luồng stream này sang luồng stream khác. Thông thường giúp người dùng thêm nội dung vào trong file. Có 2 kiểu redirection.

- Kiểu `Trancate > `

Kiểu này sẽ xóa toàn bộ nội dung cũ của file và chèn nội dung được thêm. VD như sau :

Thực hiện 2 câu lệnh chèn nội dung tới file

```sh
$ echo "first line" > /tmp/lines
$ echo "second line" > /tmp/lines
```

Kiểm tra nội dung file : 

```sh
$ cat /tmp/lines
second line
```

- Kiểu `Append >> ` 

Kiểu append sẽ chèn thêm nội dung mới vào file và không tác động gì tới nội dung cũ của file. 

```sh
$ echo "first line" > /tmp/lines
$ echo "second line" >> /tmp/lines
```

Kiểm tra nội dung file : 

```sh
$ cat /tmp/lines
first line
second line
```


### 2.  STDIN, STDOUT, STDERR và giải thích đoạn mã : `/dev/null 2>&1`

Các câu lệnh trong Linux đều có một input - SDTIN và hai loại output, standard output - STDOUT và standard error - STDERR, được gọi là các dòng dữ liệu (stream). 

STDIN, STDOUT và STDERR được diễn đạt như 1 số nguyên dương như sau : 

- standard input : 0
- standard ouput : 1
- standard error : 2


 - STDIN : các dữ liệu nhập vào như khi sử dụng lệnh `read` để nhập dữ liệu từ người dùng

![](/images/img-shell/p2-shell-04.png)

 - STDOUT : Hiển thị kết quả các câu lệnh lên màn hình. Ví dụ 
 
 
Nếu bạn đã từng đọc qua một vài shellscript thì nhất định bạn đã từng gặp một đoạn mã kỳ quặc như dưới đây:

```sh
echo hello >/dev/null 2>&1
```

Trong linux , mọi thứ đều là file, và ngay cả các `standard input`, `output` và `error` đều là file. Mỗi file đều cần phải có định danh gọi là `file descriptor`. Các file descriptor được diễn đạt như một số nguyên dương như sau : 

```sh
standard input : 0
standard ouput : 1
standard error:  2
```

`/dev/null/` là một file đặc biệt trong linux, thường được dùng để chứa các dữ liệu rác từ các input stream mà chúng ta không muốn xử lý hay muốn hiển thị nó. Nói dễ hiểu thì `/dev/null/` giống như một hố đen, chứa tất cả các dữ liệu được redirect tới nó.

Như vậy `echo hello >/dev/null 2>&1` có nghĩa là : 

 - `>/dev/null` : redirect tất cả các standard output sang `/dev/null/`. Tương đương với cách viết `1>/dev/null/`
 - `2>&1` : redirect tất cả các standard error sang `standard output`. Nhưng thời điểm này standard output đang trỏ sang `/dev/null/` nên standard error sẽ redirect sang `/dev/null/`.
 
Tóm lại lệnh này ngụ ý sẽ là không in ra màn hình tất cả các output và error bằng cách đẩy chúng vào hố đen /dev/null/. 

Chương 2 chúng tập trung tìm hiểu về kỹ thuật Redirection - chuyển luồng dữ liệu stream. Tại phần tiếp theo, chúng ta sẽ tìm hiểu về các thuật toán cơ bản trong Shell. Xin chào và hẹn gặp lại tại phần 3.