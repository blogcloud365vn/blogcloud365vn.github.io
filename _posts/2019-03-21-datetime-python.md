---
date: 2019-03-21
title: "Python Tip & Trick - Xử lý kiểu dữ liệu datetime trong Python"
categories:
  - Linux
description: Xử lý kiểu dữ liệu datetime trong Python
author: thanhnb
tags: [Python, Linux, CentOS]
type: Document
---

## Tổng quan
Gần đây mình có làm việc nhiều với kiểu dữ liệu Datetime trong Python. Vấn đề mình gặp phải là xử lý nhiều loại time format khác nhau, chuyển hóa thành dạng Datetime, chuẩn hóa thời gian lưu trữ và lưu vào database. Và sau đây, mình sẽ viết bài chia sẻ về cách mình xử lý kiểu dữ liệu Datetime trong Python. Trong bài mình sẽ sử dụng Python 3 để xử lý kiểu dữ liệu Datetime.

## Tips 1: Chuẩn hóa múi giờ sử dụng

Đầu tiên, cũng là quan trọng nhất, chuẩn hóa múi giờ sử dụng để convert các kiểu thời gian. Mình lựa chọn sử dụng chuẩn múi giờ UTC để chuẩn hóa thời gian cho cả hệ thống cũng như làm múi giờ chuẩn để xử lý các loại format thời gian khác nhau.

Tham khảo thêm về thời gian UTC [tại](https://vi.wikipedia.org/wiki/Gi%E1%BB%9D_Ph%E1%BB%91i_h%E1%BB%A3p_Qu%E1%BB%91c_t%E1%BA%BF)

## Tips 2: Convert String thành Datetime

Xử lý bằng hàm `parse`
```
import datetime
import pytz
from dateutil.parser import parse

# Dạng string time
date_string = '2019-03-20T03:41:16Z'

# Dạng datetime format
date_time_python = parse(date_string)
```

Xử lý bằng strptime
```
import datetime
import pytz
from dateutil.parser import parse

# Dạng string time
date_string = '2019-03-21 03:41:16'

# Strptime
format = '%Y-%m-%d %H:%M:%S'
date_time_python = datetime.datetime.strptime(date_string, format)
```

Lưu ý:
- Các rất nhiều format time khác nhau nên để có thể chuyển từ string thành Datetime `parse` chỉ có thể xử lý một số dạng tiêu chuẩn, nếu khác dạng tiêu chuẩn phải hiểu ra cấu trúc time string để sử dụng hàm `strp` để cắt chuỗi tạo Datetime
- Nếu kiểu dữ liệu Datetime không rõ múi giờ thì được gọi là `native date`

## Tips 3: Chuyển hóa kiểu Datetime sang Timestamp Python

Timestamp là kiểu thời gian thông dụng của hệ thông Unix, tìm hiểu thêm [tại](https://www.unixtimestamp.com/)

```
import datetime

# Dạng datetime
date_time_now = datetime.datetime.now()

# Dạng timestamp
timestamp_now = date_time_now.timestamp()
```

## Tips 4: Chuyển hóa kiểu native date sang UTC timezone

Kiểm tra kiểu timezone
```
import datetime
import pytz

# Dạng native date
date_time_now = datetime.datetime.now()
print(date_time_now.tzname())

# Dạng utc
UTC = pytz.utc
date_time_utc_now = UTC.localize(date_time_now)
print(date_time_utc_now.tzname())
```

Lưu ý: không sử dụng hàm replace, hàm replace sẽ chỉ thay đổi tzinfo không qui đổi thời gian từ múi giờ này sang múi giờ khác

## Tips 5: Convert UTC sang timezone khác
Nếu bạn ở múi giờ Việt Nam (GMT + 7), tức nếu hiện tại là 8 giờ sáng (giờ Việt Nam) thì quy ra giờ UTC tức 1 giờ sáng (giờ UTC). Vậy nếu Datetime đang ở múi giờ UTC, ta phải convert nó sáng giờ Việt Nam

```
import datetime
import pytz

# Dạng native date
date_time_now = datetime.datetime.now()
print(date_time_now.tzname())

# Dạng utc
UTC = pytz.utc
date_time_utc_now = UTC.localize(date_time_now)
print(date_time_utc_now.tzname())

# Dạng 'Asia/Ho_Chi_Minh'

VN_TZ = pytz.timezone('Asia/Ho_Chi_Minh')
date_time_vntz_now = date_time_utc_now.astimezone(VN_TZ)
print(date_time_vntz_now.tzname())
```

## Tips 6: Chuyên từ Timestamp sang Datetime
```
import datetime

date_time_now = datetime.datetime.now()
timestamp_now = date_time_now.timestamp()

# Convert timestamp thành dạng Datetime
timestamp_to_datetime = datetime.datetime.fromtimestamp(timestamp_now)
```

## Tips 7: Convert Datetime sang ISO 8601

ISO 8601 là một tiêu chuẩn quốc tế, được đưa ra bởi Tổ chức tiêu chuẩn hóa quốc tế (ISO) lần đầu tiên năm 1988, mô tả quy cách viết ngày tháng và thời gian theo cách đơn giản nhất mà máy tính có thể hiểu được. Còn đối với Python ISO 8601 Datetime là string format time thông dụng.

```
import datetime

date_time_now = datetime.datetime.now()

# Convert Datetime thành dạng ISO 8601
iso_format = date_time_now.isoformat()
```

## Nguồn

https://vi.wikipedia.org/wiki/Th%E1%BA%A3o_lu%E1%BA%ADn:M%C3%BAi_gi%E1%BB%9D

https://vi.wikipedia.org/wiki/Th%E1%BA%A3o_lu%E1%BA%ADn:M%C3%BAi_gi%E1%BB%9D

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>