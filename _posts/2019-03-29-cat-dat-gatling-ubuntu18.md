---
date: 2019-03-29
title: "Hướng dẫn cài đặt Gatling.IO trên Ubuntu 18.04"
categories:
  - Linux
description: Hướng dẫn cài đặt Gatling.IO trên Ubuntu 18.04
author: thanhnb
tags: [Gatling, Linux, CentOS]
type: Document
---

## Cài đặt Gatling.io

Trong bài mình sẽ sử dụng hệ điều hành Ubuntu 18.04 để cài Gatling tool.

Để chạy được Gatling.io, ta cần cài đặt môi trường JDK 8 (Java SE Development Kit 8)

Lưu ý: Gatling.io chỉ tương thích với phiên bản JDK 8

```
sudo apt install openjdk-8-jdk -y
```

Tải gói `Gatling.io`

```
wget https://repo1.maven.org/maven2/io/gatling/highcharts/gatling-charts-highcharts-bundle/3.0.3/gatling-charts-highcharts-bundle-3.0.3-bundle.zip
```

Với phiên bản bundle (gatling-charts-highcharts-bundle-3.0.3-bundle.zip) chúng ta chỉ cần giải nén là có thể sử dụng được

```
unzip gatling-charts-highcharts-bundle-3.0.3-bundle.zip
```

## Cấu trúc thư mục

- `bin`: Chứa các script thực hiện Gatling và Recorder
- `conf`: Chứa cấu hình cho Gatling như Log, Report, ..
- `lib`: Chứa thư viên sử dụng bởi Gatling
- `user-files`: Chứa dữ liệu người dùng
  - `simulations`: Chứa các kịch bản test (Scale file).
  - `data`: Chứa các dữ liệu mẫu (Feeder files)
  - `bodies`: Chứa template cho Request ảo
- `results`: Chứa kết quả sau những lần thực hiện test

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>