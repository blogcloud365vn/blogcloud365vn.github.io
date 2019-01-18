---
title: Thiết lập mật khẩu an toàn trên Linux
categories:
  - Linux
description: Tài liệu thiết lập mật khẩu an toàn trên Linux
author: datpt
tags: [Security, Password]
type: Document
---




## 1. Lời mở đầu.

- Hiện nay có rất nhiều các bài viết hướng dẫn về tạo chính sách mật khẩu trên Windows, tuy nhiên thì các chính sách về mật khẩu trên Linux 
thì không có nhiều, hôm nay mình sẽ giới thiệu về chính sách mật khẩu an toàn trên Linux để chúng ta có thể bảo đảm an toàn cho các tài khoản cũng như máy chủ của mình.



## 2. Các chính sách mật khẩu an toàn trên Linux.

- Tất cả các thiết lập về mật khẩu an toàn trên Linux đều được lưu tại `/etc/login.defs`, mở file `/etc/login.defs` và thực hiện các chính sách về mật khẩu :

    ```sh
    vi /etc/login.defs
    ```

**Thiết lập số ngày hế t hạn của mật khẩu**

- Với thiết lập này thì các users phải thay đổi mật khẩu của họ khi mật khẩu dùng tới giới hạn mà chúng ta đã thiết lập.

    ```sh
    # Tại dòng 25, đặt số ngày mà chúng ta muốn thiết lập, ở đây là 30
    PASS_MAX_DAYS 60
    ```
**Thiết lập thời gian tồn tại của mật khẩu**

- Với thiết lập này thì khi bạn thay đổi mật khẩu hay đặt mật khẩu cho user, mật khẩu đó sẽ tồn tại trong khoảng thời gian mà 
chúng ta quy định sau đó mới có thể đổi mật khẩu:

    ```sh
    # Tại dòng 26, đặt thời gian mật khẩu tồn tại, ở đây là 2 ngày.
    PASS_MIN_DAYS 2
    ```

**Cảnh báo thời gian hết hạn**

- Thiết lập số ngày cảnh báo trước khi mật khẩu hết hạn. Thiết lập này chỉ áp dụng đối với user được tạo mới, không có tác dụng đối với user đang tồn tại trên hệ thống. Đối với user đang tồn tại trên hệ thống chúng ta sử dụng câu lệnh `change -W <số ngày> <user>`

    ```sh
    # Tại dòng 28 thay đổi số ngày trước khi hết hạn nhận cảnh báo, ở đây là 7
    PASS_WARN_AGE 7
    ```

**Giới hạn mật khẩu đã được đặt trước đó**

- Giới hạn người dùng sử dụng password đã được đặt trước đó. Người dùng không thể thiết lập password quá số lượt quy định.

    ```sh
    vi /etc/pam.d/system-auth
    # Cạnh dòng 15, đặt số lần mật khẩu đó được thiết lập
    password     sufficient     pam_unix.so sha512 shadow nullok try_first_pass
    use_authtok remember=5
    ```

**Thiết lập độ dài mật khẩu ngắn nhất**

Thiết lập độ dài ngắn nhất của mật khẩu. Người dùng không thể đặt mật khẩu ngắn hơn số ký tự quy định

    ```sh
    # Thiết lập mật khẩu ngắn nhất là 8 kí tự
    authconfig --passminlen=8 --update
    ```

**Thiết lập độ phức tạp của mật khẩu theo lớp**

- Trong Linux có các class kí tự như sau : UpperCase / LowerCase / Digits / Others. Độ phức tạp của mật khẩu được thiết lập theo số class xuất hiện trong mật khẩu.

    ```sh
    # Cấu hình mật khẩu phải xuất hiện ít nhất 2 class
    authconfig --passminclass=2 --update
    ```

**Thiết lập số lần lặp kí tự**

- Thiết lập số lần lặp tối đa của một ký tự liền kề  đối với password mới.

    ```sh
    # Cấu hình tối da cho phép 2 kí tự lặp nhau
    authconfig --passmaxrepeat=2 --update
    ```

**Thiết lập độ phức tạp đối với mật khẩu mới**

- Yêu cầu có ít nhất một ký tự thường :

    ```sh
    authconfig --enablereqlower --update
    ```

- Yêu cầu có ít nhất một ký tự in hoa :

    ```sh
    authconfig --enablerequpper --update
    ```

- Yêu cầu có ít nhất một ký tự số :

    ```sh
    authconfig --enablereqdigit --update
    ```

**Thiết lập độ dài monotonic**

- Monotonic trong toán học được gọi là hàm số đơn điệu, là một chuỗi các số tăng hoặc giảm trong khoảng. Ở đây cấu hình hình có thể được hiểu là các ký tự tăng hoặc giảm trong khoảng được định sẵn, ví dụ thiết lập là 5 thì chỉ có thể đặt mật khẩu là `12345` hoặc `abcdf` chứ không thể đặt chuỗi dài hơn được.

    ```sh
    vi /etc/security/pwquality.conf

    # Thêm vào cuối file dòng cấu hình
    maxsequence = 5
    ```

**Thiết lập số kí tự trong mật khẩu mới không được có trong mật khẩu cũ**

    ```sh
    vi /etc/security/pwquality.conf

    # Thêm vào cuối file
    difok = 5
    ```

**Cấu hình danh sách các ký tự không được xuất hiện trong mật khẩu**

    ```sh
    vi /etc/security/pwquality.conf

    # Thêm vào cuối file
    badwords = denywords1 denywords2 denywords3
    ```

**Cấu hình thuật toán băm/mã hóa cho mật khẩu mới**


    ```sh
    # Kiểm tra thuật đang sử dụng hiện tại
    authconfig --test | grep hashing 

    # Thay đổi thuật toán sang SHA512
    authconfig --passalgo=sha512 --update
    ```

---
Thực hiện bởi [cloud365.vn](https://cloud365.vn/)
