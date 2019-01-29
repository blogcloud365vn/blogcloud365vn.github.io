---
title: Hướng dẫn thao tác cơ bản với Git
categories:
  - Linux
  - Other
description: Hướng dẫn thao tác cơ bản với Git
author: canhdx
tags: [Beginer, Linux, Git, Other]
type: Document
---

## Cài đặt 

Để sử dụng Git đương nhiên chúng ta sẽ phải cài đặt Git lên máy tính cá nhân của chúng ta để thao tác. Bản chất của việc cài đặt Git là chúng ta sẽ có một Server Git Local để thao tác. 

<h1><center><a href="https://book.git-scm.com/download/mac" target="_blank">Tải GIT về cho OSX</a></center></h1>

<h1><center><a href="https://book.git-scm.com/download/win" target="_blank">Tải GIT về cho Windows</a></center></h1>

<h1><center><a href="https://book.git-scm.com/download/linux" target="_blank">Tải GIT về cho Linux </a></center></h1>


## Tạo Repo mới trên Local 

Để tạo 1 repo mới bạn hãy tạo 1 folder. Di chuyển đến Folder này và gõ dòng lệnh:

```sh 
git init 
```

## Mô hình hoạt động cơ bản trên Git Local

Thường khi làm việc với thư mục Repo Local của bạn, có 3 trees được duy trì bởi Git (Toàn bộ được lưu trữ trong thư mục .git trên Folder của bạn).

- `Thư mục đang làm việc (Working Directory`) có chứa folder .git và các tập tin hiện tại 
- `Chỉ mục (Index)` đóng vai trò Staging area 
- Và `HEAD` trỏ đến commit gần nhất của bạn 

![](/images/img-git/trees.png)

## Sao chép (clone) một Repository 

Để clone một repository có sẵn chúng ta sử dụng câu lệnh:

```sh 
git clone /đường-dẫn-đến/repository 
```

> Lưu ý Repository này có thể là Repo Local hoặc Repo ở máy chủ khác (GitLab, GitHub, Bitbucket)

```sh 
git clone username@server:/đường-dẫn-đến/repository
```

## Thêm (add) && commit 

Sau khi làm việc chỉnh sửa với các tập tin. Chúng ta có thể đề xuất thay đổi (Thêm vào chỉ mục Index) bằng cách:

```sh 
git add <tên-tập-tin>
```

```sh 
git add *
```

Vậy là bạn đã xong Git cơ bản rồi đấy. Để thực sự commit những gì đã thay đổi chúng ta sử dụng câu lệnh sau: 

```sh 
git commit -m "Ghichú-cho-Commit"
```

Bây giờ thì những thay đổi đã được trỏ đến `HEAD` trên Git Local của bạn.

## Đẩy (push) các thay đổi 

List các thay đổi trong `Working Directory` của bạn nằm tại `HEAD` và nằm trên chính máy Local hay từ đầu bài đến giờ mình hay gọi là Git Local. Để gửi những thay đổi đó đến repository remote chúng ta thực hiện như sau:

```sh 
git push origin master
```

> master ở đây là nhánh làm việc của git, chúng ta có thể thay đổi bằng bất cứ nhánh nào "Đương nhiên sẽ cần có quyền" chúng ta sẽ nói rõ hơn ở các phần sau

Nếu repo của bạn là 1 repo mới hoàn toàn được create bằng `git init` và bạn muốn kết nối nó đến một máy chủ remote (Git Server) nào đó bạn phải thêm 

```sh 
git remote add origin <máy-chủ>
```

sau đó bạn có thể đẩy các thay đổi vào máy chủ đã chọn

## Nhánh (branches)

Ở phía trên chúng ta có sử dụng câu lệnh `git push origin master` để đẩy toàn bộ thay đổi của mình lên nhánh master. Chúng ta sẽ tìm hiểu kỹ nhánh là gì. 

Trong quá trình phát triển một sản phẩm, luôn có một nhánh chính gọi là nhánh `master`(nhánh gốc) được tạo ra mặc định khi tạo mới repo. Các nhánh con được dùng để phát triển tính năng riêng biệt. 

VD: Ở đây chúng ta có 1 sản phẩm là Portal có một nhánh là `canhdx_vol` được tạo ra để phát triển module volume của sản phẩm. Đến 1 giai đoạn nào đó sẽ `merge` lại vào nhánh `master` sau khi hoàn tất

![](/images/img-git/brand.png)

Các bước thực hiện như sau: 

- Tạo một nhánh mới với tên `canhdx_vol` và chuyển qua nhánh đó (từ nhánh master)
  ```sh 
  git checkout -b canhdx_vol
  ```

- Trở lại nhánh `master`
  ```sh 
  git checkout master
  ```

- Xóa nhánh 
  ```sh 
  git branch -d canhdx_vol
  ```

- Để các nhánh khác có thể thấy được và sử dụng lại nhánh đã có cần đẩy lên remote repo
  ```sh 
  git push origin <nhánh>
  ```

## Cập nhật (update) và Trộn (merge)

Để cập nhật toàn bộ thay đổi mới nhất từ Remote về repo local của bạn cần sử dụng câu lệnh 

```sh 
git pull
```

câu lệnh trên được sử dụng để `lấy vể(fetch)` và `trộn(merge)` các thay đổi ở Remote. 

Để merge một nhánh vào nhánh đang hoạt động sử dụng 

```sh 
git merge <nhánh>
```

Cả 2 trường hợp trên sẽ được tự động thực hiện. Nếu có xung đột xảy ra (2 nhánh cùng sửa đổi 1 file...) chúng ta sẽ phải thao tác thủ công chỉnh sửa file được hiển thị sau đó đánh dấu lại đã merge với lệnh 

```sh 
git add <tên-tập-tin>
```

Trước khi merge chúng ta cũng có thể kiểm tra các thay đổi giữa các nhánh bằng câu lênh 

```sh 
git diff <nhánh-cần-merge> <nhánh-kiểm-tra> 
```

## Gắn nhãn (Tagging)

Sử dụng lại khái niệm từng có trên SVN, việc gắn nhãn khi phát triển phần mềm là điều khá quan trọng hay còn được biết đến với cái tên dễ hình dung hơn đó là version. 

```sh 
git tag 0.0.1 6f1b2e3f1d
```

Câu lệnh phía trên sẽ tạo tag mới `0.0.1` với chuỗi `6f1b2e3f1d` là 10 ký tự đầu tiên của commit mà chúng ta muốn tham chiếu đến. Mã này có thể ít hơn nhưng cần phải là duy nhất. 

Để show mã này thì sử dụng 

```sh 
git log
```

## Thay thế các thay đổi trên Repo local

Quá trình chỉnh sửa file có thể yêu cầu revert lại thông tin của file sau khi chỉnh sửa về commit gần nhất, hay nội dung HEAD mới nhất

```sh 
git checkout -- <tên-tập-tin>
```

Trong trường hợp chúng ta muốn reset hoàn toàn repo local của mình, fetch về bản gần nhất trên remote repo và trỏ vào nhánh master 

```sh 
git fetch origin
git reset --hard origin/master
```


## Tài nguyên tham khảo 

https://book.git-scm.com/

https://help.github.com/

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>