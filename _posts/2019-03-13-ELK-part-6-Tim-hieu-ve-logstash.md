---
title: ELK part 6 - Tìm hiểu về logstash.
categories:
  - Logging
description: Tìm hiểu về logstash.
author: datpt
tags: [Logging, Linux]
type: Document
set: Gioi-thieu-ELK
set_order: 10
---

## Lời mở đầu.

Ở bài trước mình đã giới thiệu về filebeat một agent được sử dụng phổ biến cho việc thu thập các bản tin nhật ký từ các máy trạm, hôm nay mình sẽ giới thiệu về Logstash, công cụ sẽ trực tiếp nhận các bản tin được đẩy về từ filebeat sau đó xử lý và đẩy vào Elasticsearch.

## 1. Logstash là gì?

- Logstash là một công cụ mã nguồn mở thu thập dữ liệu có khả năng liên hợp theo thời gian thực. Logstash có thể hợp nhất dữ liệu từ các nguồn khác nhau và chuẩn hóa dữ liệu ở phần xử lý tiếp theo. Loại bỏ và đồng hóa tất cả dữ liệu đó trong một số use case cần phân tích và thể hiện trên biểu đồ.

- Logstash có 3 thành phần chính cũng chính là 3 bước xử lý chính của logstash đó là:
    - INPUT: Nó có thể lấy đầu vào từ TCP/UDP, các file, từ syslog, Microsoft Windows EventLogs, STDIN và từ nhiều nguồn khác. Chúng ta có thể lấy log từ các ứng dụng trên môi trường của chúng ta rồi đẩy chúng tới Logstash.
    - FILTER: Khi những log này tới Server Logstash, có một số lượng lớn các bộ lọc mà cho phép ta có thể chỉnh sửa và chuyển đổi những event này. Ta có thể lấy ra các thông tin mà ta cần từ những event log.
    - OUTPUT: Khi xuất dữ liệu ra, Logstash hỗ trợ rất nhiều các đích tới bao gồm TCP/UDP, email, các file, HTTP, Nagios và số lượng lớn các dịch vụ mạng. Ta có thể tích hợp Logstash với các công cụ tính toán số liệu (metric), các công cụ cảnh báo, các dạng biểu đồ, các công nghệ lưu trữ hay ta có thể xây dựng một công cụ trong môi trường làm việc của chúng ta.

## 2. Logstash hoạt động như thế nào.

![elk-21](/images/img-elk/elk-21.PNG)

Đường ống xử lý sự kiện của Logstash có ba giai đoạn: input → filter → output. Các đầu vào tạo ra các sự kiện, bộ lọc sửa đổi chúng và các đầu ra sẽ chuyển chúng tới nơi khác. Đầu vào và đầu ra hỗ trợ codec cho phép bạn mã hóa hoặc giải mã dữ liệu khi nó vào hoặc thoát khỏi đường dẫn mà không phải sử dụng bộ lọc riêng biệt.

![elk-22](/images/img-elk/elk-22.png)

- Chúng ta sử dụng Input để lấy dữ liệu vào Logstash. Một số đầu vào thường được sử dụng là :
    - File : đọc từ một tệp trên hệ thống, giống như lệnh UNIX tail -0F
    - Syslog : nghe trên cổng 514 nổi tiếng cho các thông báo nhật ký hệ thống và phân tích cú pháp theo định dạng RFC3164.
    - Redis : đọc từ máy chủ redis, sử dụng cả kênh redis và danh sách redis. Redis thường được sử dụng như một "broker" trong một mô hình Logstash tập trung, có hàng đợi các sự kiện Logstash từ các "shippers" từ xa.
    - Beats : xử lý các sự kiện do beats gửi.

![elk-23](/images/img-elk/elk-23.png)

- Filter là thiết bị xử lý trung gian trong đường dẫn Logstash. Chúng ta có thể kết hợp các bộ lọc với các điều kiện để thực hiện một hành động trên một sự kiện nếu nó đáp ứng các tiêu chí nhất định. Một số bộ lọc hữu ích bao gồm :
    - Grok : phân tích cú pháp và cấu trúc văn bản tùy ý - chỉnh sửa định dạng log từ client gửi về. Grok hiện là cách tốt nhất trong Logstash để phân tích cú pháp dữ liệu nhật ký không được cấu trúc thành một thứ có cấu trúc và có thể truy vấn được. Với 120 mẫu được tích hợp sẵn trong Logstash, nhiều khả năng chúng ta sẽ tìm thấy một mẫu đáp ứng nhu cầu của mình.
    - Mutate : thực hiện các phép biến đổi chung trên các trường sự kiện. Bạn có thể đổi tên, xóa, thay thế và sửa đổi các trường trong sự kiện của mình.
    - Drop : xóa hoàn toàn sự kiện, ví dụ: debug events.
    - Clone : tạo bản sao của sự kiện, có thể thêm hoặc xóa các trường.
    - Geoip : thêm thông tin về vị trí địa lý của địa chỉ IP (cũng hiển thị biểu đồ tuyệt vời trong Kibana).

![elk-24](/images/img-elk/elk-24.png)

- Các đầu ra là pha cuối cùng của đường ống Logstash. Một sự kiện có thể đi qua nhiều đầu ra, nhưng một khi tất cả xử lý đầu ra đã hoàn tất, sự kiện đã hoàn tất việc thực thi của nó. Một số đầu ra thường được sử dụng bao gồm :
    - Elasticsearch : gửi dữ liệu sự kiện tới Elasticsearch. Nếu chúng ta đang có kế hoạch để lưu dữ liệu trong một định dạng hiệu quả, thuận tiện, và dễ dàng truy vấn ... Elasticsearch là con đường để đi.
    - File : ghi dữ liệu sự kiện vào file trên bộ nhớ.
    - Graphite : gửi dữ liệu sự kiện tới graphite, một công cụ nguồn mở phổ biến để lưu trữ và vẽ đồ thị số liệu. 
    - Statsd : gửi dữ liệu sự kiện đến statsd, một dịch vụ lắng nghe và thống kê.

## 3. Một số mẫu khai báo input, filter, output.

Ví dụ về file input:

```sh
input {
  beats {
    port => 5044
    ssl => false
  }
}
```

Ví dụ về file filter:

```sh
filter {
    grok {
      match => {
        "message" => [
          "%{SYSLOGTIMESTAMP:syslog_timestamp} %{SYSLOGHOST:syslog_hostname} %{DATA:syslog_program}(?:\[%{POSINT:syslog_pid}\])?:? %{SSH_INVALID_USER:message}"
        ]
      }
      patterns_dir => "/etc/logstash/patterns/sshd"
      named_captures_only => true
      remove_tag => ["_grokparsefailure"]
      break_on_match => true
      add_tag => [ "SSH", "SSH_INVALID_USER" ]
      add_field => { "event_type" => "SSH_INVALID_USER" }
      overwrite => "message"
    }
}

# Grok Filter for SSH Failed Password
filter{
    grok {
      match => {
        "message" => [
          "%{SYSLOGTIMESTAMP:syslog_timestamp} %{SYSLOGHOST:syslog_hostname} %{DATA:syslog_program}(?:\[%{POSINT:syslog_pid}\])?:? %{SSH_FAILED_PASSWORD:message}"
        ]
      }
      patterns_dir => "/etc/logstash/patterns/sshd"
      named_captures_only => true
      remove_tag => ["_grokparsefailure"]
      break_on_match => true
      add_tag => [ "SSH", "SSH_FAILED_PASSWORD" ]
      add_field => { "event_type" => "SSH_FAILED_PASSWORD" }
      overwrite => "message"
    }
}

filter {
# Grok Filter for SSH Password Accepted

    grok {
      match => {
        "message" => [
          "%{SYSLOGTIMESTAMP:syslog_timestamp} %{SYSLOGHOST:syslog_hostname} %{DATA:syslog_program}(?:\[%{POSINT:syslog_pid}\])?:? %{SSH_ACCEPTED_PASSWORD}"
        ]
      }
      patterns_dir => "/etc/logstash/patterns/sshd"
      named_captures_only => true
      remove_tag => ["_grokparsefailure"]
      break_on_match => true
      add_tag => [ "SSH", "SSH_ACCEPTED_PASSWORD" ]
      add_field => { "event_type" => "SSH_ACCEPTED_PASSWORD" }
    }
}
```

Ví dụ về file output:

```sh
output {
     elasticsearch {
       hosts => ["localhost:9200"]
       sniffing => true
       index => "%{[@metadata][beat]}-%{+YYYY.MM.dd}"
     }
}
```

## Tổng kết.

Như vậy ở bài này mình đã tổng quan về logstash và cách hoạt động của nó, ở series này mình đã giới thiệu đa số các thành phần cần sử dụng để triển khai ELk stack, ở bài lần sau mình sẽ hướng dẫn mọi người cách setup biểu đồ trên Kibana để có cái nhìn trực quan về những sự kiện thu thập được từ client.

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>