---
title: "Tìm hiểu về Docker - Phần 7 - Docker Volume"
categories:
  - Container
description: Tìm hiểu về Docker - Phần 7 - Docker Volume
author: huytm
tags: [Docker, Centos]
type: Document
---

## Mở đầu

Ở các phần trước mình đã giới thiệu cơ bản về các thành phần trong kiến trúc tổng quan của Docker bao gồm: *Docker Daemon, Docker Container, Docker Image...* cũng như các câu lệnh thao tác cơ bản với Container, Image.

Ở phần này mình sẽ giới thiệu một thành phần vô cùng quan trọng trong kiến trúc của Docker, đó chính là **Docker Volume**.

## Docker Volume là gì ?

**Docker Volume** là một tính năng quản lý data của Docker. Data ở đây có thể hiểu là các file được sinh ra trong quá trình chạy Ứng dụng, ví dụ như file log, file data, file report ... 

Mặc định khi một container được khởi chạy, data trong quá trình vận hành  được chứa ở *writeable layer* và sẽ bị mất đi khi container bị xóa. 
. Ngoài ra thì một nhược điểm nữa đó là data ở container này khó có thể chia sẻ data với các container khác.

Vậy để giải quyết các vấn đề này, Docker đưa ra một cơ chế để quản lý data của các Container gọi là **Docker Volume**

Về bản chất, Docker volume là một cơ chế cho phép lưu trữ các data của *Container* vào *Docker Host* bằng cách **mount** một folder từ Docker Container vào Docker Host.

Bằng việc mount này, data trong container giờ đây sẽ được an toàn hơn, dễ dàng chia sẻ giữa các container với nhau hơn. Một số folder chứa setting hay log có thể được đọc dễ dàng hơn trong quá trình troubleshoot các Container.

## Các kiểu mount của Docker Volume

<p align="center">
<img src="/images/img-docker/docker7/types-of-mounts.png" title="Nguồn: docs.docker.com">
</p>

Có 3 kiểu mount của Docker Volume đó là:

- **Volumes**: Mount-point sẽ nằm ở */var/lib/docker/volumes/* của Docker Host.
- **bind mounts**: Mount-point có thể nằm ở bất kỳ đâu Docker Host.
- **tmpfs mounts**: Data sẽ được lưu và memory của Docker Host và không bao giờ được ghi vào file system

**-v và -mount flag**

Ban đầu -v hoặc -volume flag được dùng cho standalone container và -mount flag được dùng cho swarm services. Tuy nhiên từ phiên bản Docker 17.06 bạn có thể sử dụng -mount flag cho standalone container. Nói chung điểm khác biệt duy nhất chính là là cú pháp. Trong khi -v flag, các option được gói gọn làm một thì -mount flag lại phân chia chúng rõ ràng hơn với từng option các nhau bởi dấu phẩy. Trong bài viết này mình sẽ sử dụng cả 2 option là -v và -mount.

## Volumes

Về hoạt động Volume tương tự như Bind mounts, nhưng **Volume** được quản lý bởi Docker. Trong khi mount file hoặc thư mục trong container với đường dẫn tuyệt đối của Dockerhost.

Volume khi tạo ra sẽ nằm ở thư mục */var/lib/docker/volumes/*

Volume cũng support cơ chế của volume drivers, cho phép lưu trữ dữ liệu tới một server remote hoặc cloud ...

**Sử dụng Volume khi nào**

- Khi chia sẻ dữ liệu giữa nhiều container đang chạy.
- Lưu dữ liệu tới một server remote hoặc cloud.
- Khi cần backup, restore hoặc migrate dữ liệu từ Docker Host này sang Docker Host khác.


--
## Tổng kết

Như vậy trong bài viết này mình đã giới thiệu một số các command cơ bản để làm việc với Image. Ở phần tiếp theo mình sẽ giới thiệu một khái niệm quan trọng trong Docker đó là **Docker Volume**.

Cảm ơn các bạn đã theo dõi serie của mình ! :D

>"if you have knowledge let others light their candles in it"

---

### Tài liệu tham khảo
[https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/){:target="_blank"}

---

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>

