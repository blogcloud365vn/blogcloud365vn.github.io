---
title: "Tìm hiểu về Docker - Phần 5 - Các câu lệnh với container"
categories:
  - Container
description: Tìm hiểu về Docker - Phần 5 - Các câu lệnh với container
author: huytm
tags: [Docker, Centos]
type: Document
---

## Mở đầu

Ở các phần trước mình đã giới thiệu tổng quan về **Docker**, các thành phần cấu tạo nên Docker. Image là gì ? Container là gì ? Đó cũng chính là điều kiện tiên quyết khi các bạn đọc đến bài này của mình. 

Trong bài viết này mình sẽ tập trung viết về một số command cơ bản để thao tác với container.

## Một số lưu ý nhỏ

**1. Trong bài viết mình sẽ viết hai command trong một block chẳng hạn như thế này** 

```
docker run httpd
docker container run httpd
```

Cả hai command trên đều tương tự như nhau. Các bạn có thể chọn một trong hai. Bản thân mình thì thường sử dụng command trên cho nó ngắn gọn :v.

Vì đâu có sự tương đương này? Là do **Docker v1.13** đã tái cấu trúc lại CLI. Thông tin cụ thể hơn các bạn có thể theo dõi tại [đây](https://blog.docker.com/2017/01/whats-new-in-docker-1-13/){:target="_blank"}

**2. Một số option hay sử dụng trong bài viết:**

**-d detach container** (Có thể hiểu là chạy ngầm container cũng được)

> Ví dụ nếu không có option này chạy container, cửa sổ dòng lệnh phải giữ phiên. Nếu kết thúc cửa sổ dòng lệnh, container cũng stop theo.

**-t Tạo một pseudo-TTY**

> Ví dụ nếu không có option này khi attach lại container thì sẽ không có cửa sổ dòng lệnh

**-i giữ lại một STDIN kể cả khi detach**

>Ví dụ nếu không có option này khi attach lại container thì khi gõ command sẽ không có kết quả trả về

> Theo khuyến cáo thì nên sử dụng cả 3 option này theo dạng `-itd` đối với các command như: *docker run*, *docker create* ... 

**3. Docker làm việc, tương tác với các thành phần qua ID hoặc NAME** 


## Nhóm các Command liên quan đến Lifecycle của Container

#### 1. Hiển thị list các container 

- Ý nghĩa: Hiển thị list danh sách các container đang được Docker quản lý

```bash
docker ps [OPTIONS]
docker container ls [OPTIONS] 
```

- Ví dụ list các container đang chạy (Up)

```bash
docker ps
```

- Ví dụ list tất cả các container

```bash
docker ps -a
```


#### 2. Tạo mới một container

- Ý nghĩa: Tạo mới một container nhưng không start

- Cú pháp 

```
docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
docker container create [OPTIONS] IMAGE [COMMAND] [ARG...]
```

- Ví dụ

```
docker create -itd centos
```

#### 3. Chạy một container

- Ý nghĩa: Tạo mới một container và start container đó luôn

- Cú pháp 

```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

- Ví dụ

```
docker run -itd centos
```

#### 4. Xóa container

- Ý nghĩa: Xóa Container

- Cú pháp 

```
docker rm [OPTIONS] CONTAINER [CONTAINER...]
docker container rm [OPTIONS] CONTAINER [CONTAINER...]
```

- Ví dụ xóa một container đã stop

```
docker rm my-container
```

- Ví dụ xóa một container chưa stop

```
docker rm -f my-container
```

- Ví dụ xóa tất cả các container

```
docker rm -f $(docker ps -aq)
```

#### 5. Xóa tất cả container đang stop

- Ý nghĩa: Xóa tất cả các container
đang stop
- Cú pháp 

```
docker prune [OPTIONS]
docker container prune [OPTIONS]
```

- Ví dụ 

```
docker prune
```

## Nhóm các Command quản lý trạng thái của container

#### 1. Start container

- Ý nghĩa: Start một container

- Cú pháp 

```
docker start [OPTIONS] CONTAINER [CONTAINER...] 
docker container start [OPTIONS] CONTAINER [CONTAINER...]
```

- Ví dụ

```
docker start my-container
```

#### 2. Stop container

- Ý nghĩa: Stop một container

- Cú pháp 

```
docker stop [OPTIONS] CONTAINER [CONTAINER...] 
docker container stop [OPTIONS] CONTAINER [CONTAINER...]
```

- Ví dụ

```
docker stop my-container
```

#### 3. Restart container

- Ý nghĩa: Restart một container

- Cú pháp 

```
docker restart [OPTIONS] CONTAINER [CONTAINER...] 
docker container restart [OPTIONS] CONTAINER [CONTAINER...]
```

- Ví dụ

```
docker restart my-container
```

#### 4. Pause container

- Ý nghĩa: Tạm dừng lại một container

- Cú pháp 

```
docker pause CONTAINER [CONTAINER...]
docker container pause CONTAINER [CONTAINER...
```

- Ví dụ

```
docker pause my-container
```

#### 5. Unpause container

- Ý nghĩa: Tiếp tục lại một container đang tạm dừng

- Cú pháp 

```
docker unpause CONTAINER [CONTAINER...]
docker container unpause CONTAINER [CONTAINER...]
```

- Ví dụ

```
docker unpause my-container
```

#### 6. Kill container

- Ý nghĩa: Kill một hoặc nhiều container (Kill ở đây giống như stop, container chỉ bị stop chứ không mất đi)

- Cú pháp 

```
docker kill [OPTIONS] CONTAINER [CONTAINER...]
docker container kill [OPTIONS] CONTAINER [CONTAINER...]
```

- Ví dụ

```
docker kill my-container
```

## Nhóm các Command hiển thị thông tin của container

#### 1. Log container

- Ý nghĩa: Hiển thị log của container

- Cú pháp 

```
docker logs [OPTIONS] CONTAINER
docker container logs [OPTIONS] CONTAINER
```

- Ví dụ

```
docker logs my-container
```


#### 2. Hiển thị thông tin container

- Ý nghĩa: Hiển thị thông tin chi tiết của container 

- Cú pháp 

```
docker inspect [OPTIONS] CONTAINER [CONTAINER...]
docker container inspect [OPTIONS] CONTAINER [CONTAINER...]
```

- Ví dụ

```
docker inspect my-container
```

#### 3. Hiển thị tài nguyên của contaier

- Ý nghĩa: Hiển thị tài nguyên đang sử dụng của contaier

- Cú pháp 

```
docker stats [OPTIONS] [CONTAINER...]
docker container stats [OPTIONS] [CONTAINER...]
```

- Ví dụ

```
docker stats my-container
```

#### 4. Hiển thị các tiến trình đang chạy trong container

- Ý nghĩa: Hiển thị các tiến trình đang chạy trong container

- Cú pháp 

```
docker top CONTAINER [ps OPTIONS]
docker container top CONTAINER [ps OPTIONS]
```

- Ví dụ

```
docker top my-container
```

#### 5. Hiển thị các port được map

- Ý nghĩa: Hiển thị các port mapping hoặc một port mapping cụ thể

- Cú pháp 

```
docker port CONTAINER [PRIVATE_PORT[/PROTO]]
docker container port CONTAINER [PRIVATE_PORT[/PROTO]]
```

- Ví dụ

```
docker port my-container
```

#### 6. Hiển thị các thay đổi về filesystem trong container

- Ý nghĩa: Hiển thị các thay đổi về filesystem trong container từ lúc create container

- Cú pháp 

```
docker diff CONTAINER
docker container diff CONTAINER
```

- Ví dụ

```
docker diff my-container
```

## Nhóm các Command thực thi với container

#### 1. Attach container

- Ý nghĩa: Attach một màn hình cho phép nhập input và hiển thị output đối với một container đang chạy

- Cú pháp 

```
docker attach [OPTIONS] CONTAINER
docker container attach [OPTIONS] CONTAINER
```

- Ví dụ

```
docker attach my-container
```

#### 2. Thực thi một câu lệnh trong container

- Ý nghĩa: Thực thi một câu lệnh trong container đang chạy

- Cú pháp 

```
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
docker container exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

- Ví dụ

```
docker exec my-container free -m
```

#### 3. Update resource cho container

- Ý nghĩa: Update resource cho container, mở rộng, limit tài nguyên cho container

- Cú pháp 

```
docker update [OPTIONS] CONTAINER [CONTAINER...]
```

- Ví dụ

```
docker update -m 512M --memory-swap 1024M my-container
```

#### 4. Copy file giữa host và container

- Ý nghĩa: Copy file giữa Docker host và container

- Cú pháp 

```
docker cp [OPTIONS] SRC_PATH CONTAINER:DEST_PATH
docker container cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH
```

- Ví dụ

```
docker cp my-container:/opt/myfile /opt/
docker cp myfile1.txt my-container:/opt/
```

#### 5. Đổi tên container 

- Ý nghĩa: Đổi tên một container 

- Cú pháp 

```
docker rename CONTAINER NEW_NAME
docker container rename CONTAINER NEW_NAME
```

- Ví dụ

```
docker rename my-container my-container1
```

#### 6. Export toàn bộ filesystem trong container 

- Ý nghĩa: Export toàn bộ filesysystem trong container thành một file **tar**

- Cú pháp 

```
docker export [OPTIONS] CONTAINER
docker container export [OPTIONS] CONTAINER
```

- Ví dụ

```
docker export -o huytm.tar my-container
```

#### 7. Tạo một image từ container đang chạy 

- Ý nghĩa: Tạo một image từ container đang chạy

- Cú pháp 

```
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

- Ví dụ

```
docker container commit my-container my_new_image
```

---

## Tổng kết

Như vậy trong bài viết này mình đã giới thiệu một số các command cở bản để làm việc với container. Mỗi commnad có các option - hay còn gọi là flag đi kèm. Trong phạm vi bài viết mình chưa thể giới thiệu sâu về các option này, mình sẽ có một bài viết cụ thể cho từng command sau.

Bài viết này chỉ giới thiệu các command cơ bản đủ để thao tác và tìm hiểu về Docker. Trong bài viết tiếp theo mình sẽ giới thiệu về các command cơ bản hay sử dụng đối với image.

>"if you have knowledge let others light their candles in it"

---

### Tài liệu tham khảo
[https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/){:target="_blank"}

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>

