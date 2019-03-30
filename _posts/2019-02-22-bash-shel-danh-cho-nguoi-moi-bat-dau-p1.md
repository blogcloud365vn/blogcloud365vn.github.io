---
date: 2019-02-22
title: "Bash Shell dành cho người mới bắt đầu - Chương 1"
categories:
  - Shell
description: Bash Shell dành cho người mới bắt đầu
author: manhdv
tags: [Beginer, Linux, Shell]
type: Document
---

## Lời nói đầu

Đối với bất kỳ những ai đã và đang làm việc với hệ thống Linux thì chắc chắn đều thấy sự hiệu quả của nhứng script Bash Shell. Việc sử dụng script Bash shell giúp các công việc lặp đi lặp lại trên Linux được thực hiện hiệu quả, ít sai sót. Tuy nhiên để viết các script Bash shell ngắn, gọn và có thể tận dụng được trên nhiều môi trường thì chúng ta cần có những kiến thức và lượng thực hành nhất định.

Chuỗi bài viết về Bash Shell sau được biên dịch lại từ cuốn **Bash Notes for Professionals** thuộc bản quyền của trang https://goalkicker.com, đi kèm với những thực hành của mình trong quá trình học về Shell. Hi vọng chuỗi bài đây sẽ giúp các bạn có những kiến thức nền tảng vững chắc về ngôn ngữ script thú vị này.


## 1. Bash Shell:  Hello World

### Chế độ Shell tương tác (Interactive Shell)

Chúng ta có thể hiểu đơn giản Shell interactive là dạng sử dụng câu lệnh trực tiếp trên môi trường Unix như ví dụ dưới.

Ví dụ, sử dụng Bash để in ra output là **Hello World** như sau :

```sh
echo "Hello World"
#> Hello World 
```

### Chế độ Shell không tương tác (Non-Interactive Shell)

Thay vì thực hiện từng câu lệnh Bash một. Chúng ta có thể tổ hợp chúng vào một file script và có thể sử dụng lại nhiều lần. 

Ví dụ, tạo một script **Hello World**

- Tạo một file với tên là `hello-world.sh`

```sh
touch hello-world.sh
```

- Phân quyền thực thi được với câu lệnh 

```sh
chmod +x hello-world.sh
```

- Thêm nội dung sau vào script
```sh
#!/bin/bash
echo "Hello World"
```
	 - Dòng 1 : Dòng đầu tiên của script phải bắt đầu với ký tự `#!`, theo đúng cấu trúc shebang1. Cấu trúc shebang sẽ chỉ cho OS chạy chương trình `/bin/bash` để thực hiện các nội dung của script. VD : `/bin/bash hello-world`
	 - Dòng 2 : Sử dụng câu lệnh `echo` để ghi dòng `Hello World` ra output. 

- Thực hiện chạy sript `hello-world.sh` theo 1 trong 3 cách sau : 
```sh
./hello-world.sh 
/bin/bash hello-world.sh
bash hello-world.sh 
```

## 2. Sử dụng các biến trong Linux

Tạo một file mới tên gọi `hello.sh` với nội dung bên dưới và cấp quyền thực thi `chmod +x hello.sh`

Thực hiện chạy sript với cú pháp `./hello.sh`. Output mong đợi sẽ là :

```sh
Hello, World
```

Nội dung  script như sau :

```sh
#!/usr/bin/env bash
who_variable="World"
printf "Hello, %s\n" "$who_variable"
```

Phân tích từng dòng của script sẽ như sau : 

- Dòng 1 : `#!/usr/bin/env bash` : Cú pháp shebang chỉ ra chương trình cần thực thi với Bash shell
- Dòng 2 : Gán biến `who_variable` có nội dung là `World`.
- Dòng 3 : 
	 - `printf` : câu lệnh in 
	 - `%s` : format chỉ ra rằng biến phía sau phải là dạng string
	 - `\n` : khi có thêm ký tự thì sẽ xuống dòng mới
	 - `$who_variable` : biến được khai báo ở dòng trên
	 
**Chú ý** : Các biến cần được đặt bên trong dấu `""`. Để biết thêm tầm quan trọng của việc này, tham khảo (link sau)[https://unix.stackexchange.com/questions/171346/security-implications-of-forgetting-to-quote-a-variable-in-bash-posix-shells]

Ngoài cách gọi trực tiếp biến bằng tên, chúng ta có thể sử dụng biến thay thế là `$1`. Biến này đại diện cho đối số ở dòng đầu tiên và biến `$1` vẫn phải là dạng string. 	 

## 3. Truyên tham số vào biến với User Input

Các biến còn có thể được truyền vào trực tiếp từ người dùng như sau :

```sh
#!/usr/bin/env bash
echo "Xin chao, ban ten la gi?"
read name
echo "Xin chao, $name."
```

Khi script chạy tới dòng thứ 2, câu lệnh `read` sẽ đọc dữ liệu truyền vào từ người dùng, sau đó gán dữ liệu đó vào biến `name`. 

Kết quả như sau :

```sh
Xin chao, ban ten la gi?
Manh
Xin chao, Manh.
```

## 4. Tầm quan trọng của các dấu nháy (quoting) với String

Việc sử dụng dấu nháy rất quan trọng trong việc thể hiện string trong bash. 

Có 2 dạng dấu nháy :

- Weak quoting : dấu nháy kép "
- Strong quoting : dấu nháu đơn '

### TH1 : Weak quoting (nháy kép) 
 
Sử dụng nháy kép khi bạn muốn bash thực thi các biến String được truyền vào. Ví dụ, với script như sau :

```sh
#!/usr/bin/env bash
world="World"
echo "Hello $world"
```

Output hiện ra sẽ là : `Hello World`

### TH2 : Strong quoting (nháy đơn)

Sử dụng nháy đơn khi bạn muốn giữ nguyên nội dung trong dấu nháy. Ví dụ :

```sh
#!/usr/bin/env bash
world="World"
echo 'Hello $world'
```

Output hiện ra sẽ là : `Hello $world`

Hoặc bạn có thể sử dụng dâu `\` để thể hiện đây là một String thông thường.

```sh
#!/usr/bin/env bash
world="World"
echo "Hello \$world"
```

## 5. Chế độ **Debug** trong Shell

Để có thể theo dõi quá trình thực thi script nhằm hiểu rõ hơn về cách script thực hiện, hoặc tìm lỗi trong script, chúng ta có thể dùng chế độ **Debug**.
 
Để thực hiện việc debug, cần thêm tổ hợp ký tự `-x` đằng sau các mệnh lệnh run.
 
### Ví dụ 1

Bật mode debug với script `hello-world.sh` ở phần 1.3 nhằm theo dõi quá trình thực thi script

```sh
bash -x hello-world.sh

+ echo 'Xin chao, ban ten la gi?'
Xin chao, ban ten la gi?
+ read name
Manh
+ echo 'Xin chao, Manh.'
Xin chao, Manh.
```

### Ví dụ 2

Bật mode debug để tìm lỗi có trong script `hello.sh` sau :

```sh
#!/bin/bash
echo "Hello World\n"
adding_string_to_number="s"
v=$(expr 5 + $adding_string_to_number)
```

Output hiển thị sẽ là :

```sh
Hello World\n
expr: non-integer argument
```

Tuy nhiên, nội dung output như vậy là chưa đủ để chúng ta tìm ra lỗi của script. Hãy thử bật mode debug để tìm lỗi :

```sh
bash -x hello.sh

+ echo 'Hello World\n'
Hello World\n
+ adding_string_to_number=s
++ expr 5 + s
expr: non-integer argument
+ v=
```

Có thể thấy do `s` không phải là một chữ số vậy nên toán tử không thể thực hiện được.


Trên đây là nội dung của Chương 1, hẹn gặp các bạn tại Chương 1 phần 2 vào cuối tuần sau. Xin cảm ơn !
 