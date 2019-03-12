---
title: "Tìm hiểu về Docker - Phần 6 - Các câu lệnh với image"
categories:
  - Container
description: Tìm hiểu về Docker - Phần 6 - Các câu lệnh với image
author: huytm
tags: [Docker, Centos]
type: Document
---

## Mở đầu

Ở các phần trước mình đã giới thiệu về các lệnh cơ bản để thao tác với Container. Ở phần này mình sẽ giới thiệu một số lệnh cơ bản thao tác với Image.

Trong Docker, *Image* dùng để  chạy Container. Các bạn có thể tưởng tượng Image giống như file *GHOST* khi cài Windows và Container chính là Windows được cài hoàn chỉnh từ file GHOST đó.

Giống như file GHOST, bản thân mỗi image nên được đóng gói một số thư viện nhất định, vừa đủ để  khởi tạo container. Hoặc đóng sẵn thêm một số phần mềm có sẵn tùy vào mục đích sử dụng của người dùng.

Quá trình build một Image mình đã giới thiệu trong [phần 4](https://blog.cloud365.vn/container/tim-hieu-docker-phan-4/){:target="_blank"} của serie này.

## Một số chú ý nho nhỏ

Cũng giống như bài viết trước. Để thực hiện một thao tác đối với Image chúng ta cũng có thể có nhiều cách (nhiều command) giống như Container. Vậy nên bài viết này mình cũng sẽ viết nhiều command trong một block code.

Với mỗi block code có nhiều command, các command này có ý nghĩa như nhau. Vậy nên các bạn việc sử dụng command nào cũng được, tùy hoàn cảnh và cách sử dụng của mỗi người nhé :v


## Các command cơ bản với Image

#### 1. Hiển thị list images đang có

- Ý nghĩa: Hiển thị list các Image đang có

```bash
docker images [OPTIONS] 
docker image ls [OPTIONS] 
```

Khi chạy một trong hai command trên sẽ hiển thị list các image có trong Docker host.

```
REPOSITORY       TAG        IMAGE ID            CREATED             SIZE
huytm/httpd      1.0        aea3873b1da1        7 minutes ago       458MB
huytm/httpd      2.0        aea3873b1da1        7 minutes ago       458MB
```

Trong đó 
- **REPOSITORY**: là kho chứa của một Image. Image này có thể có nhiều TAG

- **TAG**: TAG có thể hiểu là version của Image cũng được

- **IMAGE ID**: ID của Image

- **CREATED**: Thời gian tạo Image

- **SIZE**: Dung lượng của Image

#### 2. Tải một image về từ Registry

- Ý nghĩa: Tải một image về từ Registry (Mặc định là DockerHub)

- Cú pháp 

```
docker image pull [OPTIONS] NAME[:TAG|@DIGEST]
```

- Ví dụ

```
docker image pull centos
```

#### 3. Đẩy một image lên Registry

- Ý nghĩa: Đẩy một image về lên Registry (Mặc định là DockerHub)

- Cú pháp 

```
docker image push [OPTIONS] NAME[:TAG]
```

- Ví dụ đẩy một image lên DockerHub

Trước hết bạn phải tạo một tài khoản của DockerHub. Tiếp theo là tên Image phải bắt đầu bởi tên tài khoản DockerHub của bạn (Chẳng hạn *huytm/centost_httpd* )

```
 docker image push huytm/centost_httpd:1.0
```

Nhập *username / password* của **DockerHub** và đợi một chút để image được đẩy lên.

Kết quả như sau: 

<p align="center">
<img src="/images/img-docker/docker6/push.png">
</p>


#### 4. Hiển thị chi tiết của Image

- Ý nghĩa: Hiển thị chi tiết của một image

- Cú pháp 

```
docker inspect [OPTIONS] IMAGE
docker image inspect [OPTIONS] IMAGE
```

- Ví dụ

```
docker inspect my_image
```

#### 5. Hiển thị lịch sử của image

- Ý nghĩa: Hiển thị lịch sử của image

- Cú pháp 

```
docker image history [OPTIONS] IMAGE
```

- Ví dụ

```
docker image history my_image
docker image history my_image:<TAG>
```

#### 6. Tạo một Image TAG mới

- Ý nghĩa: Tạo một Image có TAG mới từ một Image đang có

- Cú pháp 

```
docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

- Ví dụ

```
docker image tag my-image:TAG1 my-image:TAG2
```

#### 7. Save một image thành môt file tar

- Ý nghĩa: Save một image thành một file *.tar*. File này sẽ bao gồm các Layer sử dụng để tạo image đó, các file data dạng *.json* ...

- Cú pháp 

```
docker image save [OPTIONS] IMAGE [IMAGE...]
```

- Ví dụ 

```
docker image save -o /opt/my_image_file.tar my_image
```

#### 8. Tạo lại image bằng việc load lại file tar

- Ý nghĩa: Sử dụng file tar vừa save được ở câu lệnh *docker save* để tạo lại một image

- Cú pháp 

```
docker image load [OPTIONS]
```

- Ví dụ 

```
docker image load -i my_image_file.tar
```

#### 9. Xóa image

- Ý nghĩa: Xóa một hoặc nhiều image
 
- Cú pháp 

```
docker rmi [OPTIONS] IMAGE [IMAGE...]
docker image rm [OPTIONS] IMAGE [IMAGE...]
```

- Ví dụ 

```
docker image rm my_image
```

---

## Tổng kết

Như vậy trong bài viết này mình đã giới thiệu một số các command cơ bản để làm việc với Image. Ở phần tiếp theo mình sẽ giới thiệu một khái niệm quan trọng trong Docker đó là **Docker Volume**.

Cảm ơn các bạn đã theo dõi serie của mình ! :D

>"if you have knowledge let others light their candles in it"

---

### Tài liệu tham khảo
[https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/){:target="_blank"}

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>

