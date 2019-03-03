---
date: 2019-03-03
title: "Hướng dẫn kiểm tra dung lượng Ram trên hệ điều hành Linux"
categories:
  - Linux
description: Hướng dẫn kiểm tra dung lượng Ram trên hệ điều hành Linux
author: thanhnb
tags: [Linux, CentOS7]
type: Document
---

## Tổng quan
Linux hỗ trợ rất nhiều command khác nhau để kiếm tra dung lượng ram thực tế cũng như dung lượng ram đã sử dụng. Ví dụ như câu lệnh `free` hiển thị tổng dung lượng bộ nhớ vật lý (Physical memory) và dung lượng bộ nhớ chuyển đổi (Swap memory) đã sử dụng. Hoặc câu lệnh `top` để xem dung lượng ram đã sử dụng thời gian thực (realtime)

## Kiểm tra số Ram có thể sử dụng
File `/proc/meminfo` chứa thông tin cơ bản về bộ nhớ ram đã sử dụng trên các hệ điều hành Linux. File này cũng được sử dụng bởi các công cụ report như `free` để kiểm tra về bộ nhớ đã sử dụng (cả physical và swap) trên hệ thống cũng như bộ nhớ chia sẻ (shared memory) và bộ nhớ đệm sử dụng bởi kernel (buffer memory)

Cách kiểm tra
```
cat /proc/meminfo
less /proc/meminfo
egrep --color 'Mem|Cache|Swap' /proc/meminfo
```

Kết quả, ở đây mình chỉ tập trung vào một số tham số đơn giản thôi nhé
```
$ egrep --color 'Mem|Cache|Swap' /proc/meminfo
MemTotal:        1882868 kB
MemFree:         1584544 kB
MemAvailable:    1615004 kB
Cached:           163676 kB
SwapCached:            0 kB
SwapTotal:             0 kB
SwapFree:              0 kB
```

Giải thích
- `MemTotal`: Tổng số Ram hiện có (Đơn vị kilobyte). Ở đây, 1882868 kB tức 1838 MB và bằng 1,838 GB. Lưu ý số ram hiện có sẽ luôn nhỏ hơn số ram vật lý. Ở đây, mình tạo một Cloud VPS có dung lượng ram thực là 2 GB nhưng số ram hiện có là 1,838 GB. Lý do tại sao mình sẽ giải thích bên dưới.
- `MemFree`: Số ram có thể sử dụng tại thời điểm kiếm tra, giá trị bằng tổng LowFree + HighFree (cùng file)
- `MemAvailable`: Tổng số Ram có thể sử dụng cho việc khởi động một ứng dụng mới mà không phải sử dụng đến Ram Swap.
- `Cached`: Dung lượng file được cache trên ram (Không bao gồm `SwapCached`)
- `SwapCached`: Dung lượng bộ nhớ vật lý (Ram Physical) được cache lại trong bộ nhớ chuyển đối (Ram Swap).
- `SwapTotal`: Tổng dung lượng Ram Swap hiện có
- `SwapFree`: Số Ram Swap có thể sử dụng

Kiếm tra dung lượng bộ nhớ bằng câu lệnh `free`
```
free -h
```

Kết quả
```
[root@thanhnb ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:           1.8G         81M        1.5G         16M        223M        1.5G
Swap:            0B          0B          0B
```

Lưu ý:
- Số ram trống tại thời điểm kiểm tra bằng 1.5 GB (`free`) nhưng thực tế bạn có thể sử dụng nhiều hơn thế. Tổng số ram trống thực sự sẽ bằng `free` + `buff/cache`. Vậy ở đây, số ram thực sự có thể sử dụng được sẽ bằng 1.723 GB.
- Lượng ram được `buff/cache` thường được sử dụng để cải thiện hiệu năng đọc ghi ổ đĩa. Vì bộ ram sẽ có tốc độ truy vấn, đọc ghi cao hơn rất nhiều so với tốc độ truy vấn, đọc ghi ổ đĩa nên hệ điều hành sử dụng lượng ram còn trống để cài thiện hiệu năng.


## Một số câu lệnh kiểm tra thông dụng
Hiện thị dung lượng bộ nhớ có thể sử dụng bằng câu lệnh free (dạng megabytes):
```
free -m
```

Kết quả
```
[root@thanhnb ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           1838          79        1536          16         222        1577
Swap:             0           0           0
```

Hiện dạng với đơn vị gigabytes
```
free -h
```

Kết quả
```
[root@thanhnb ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:           1.8G         79M        1.5G         16M        223M        1.5G
Swap:            0B          0B          0B
```

Hiện thị dụng lượng bộ nhớ có thể sử dụng bằng câu lệnh `vmstat`
```
vmstat -s
```

Kết quả
```
[root@thanhnb ~]# vmstat -s
      1882868 K total memory
        80252 K used memory
        93068 K active memory
       119820 K inactive memory
      1574280 K free memory
         2600 K buffer memory
       225736 K swap cache
            0 K total swap
            0 K used swap
            0 K free swap
         1581 non-nice user cpu ticks
          102 nice user cpu ticks
         1300 system cpu ticks
       205325 idle cpu ticks
          147 IO-wait cpu ticks
            0 IRQ cpu ticks
            2 softirq cpu ticks
           67 stolen cpu ticks
       157716 pages paged in
        42562 pages paged out
            0 pages swapped in
            0 pages swapped out
       136715 interrupts
       194901 CPU context switches
   1551621186 boot time
        17126 forks
```

## Giải thích lý do tại sao số ram hiện có lại nhỏ hơn số ram vật lý.

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:           1838          80        1535          16         223        1576
Swap:             0           0           0
```
Như mình đã nói ở trên, mình tạo một Cloud VPS có dung lượng 2 GB nhưng số ram mình có thể sử dụng chỉ là 1,838 GB. Có phải hệ điều hành đã nhận thiếu ram hoặc nhà cung cấp cấp phát thiếu ram cho Cloud VPS?. Thực ra hệ điều hành Linux đã nhận đủ số ram là 2 GB nhưng một phần ram đã bị chiếm dụng bởi Kernel Linux.

Thực hiện câu lệnh
```
dmesg | grep -i memory
```

Kết quả
```
[root@thanhnb ~]# dmesg | grep -i memory
[    0.000000] Base memory trampoline at [ffff9f2000099000] 99000 size 24576
[    0.000000] Reserving 161MB of memory at 640MB for crashkernel (System 0: 2047MB)
[    0.000000] Early memory node ranges
[    0.000000] PM: Registered nosave memory: [mem 0x0009f000-0x0009ffff]
[    0.000000] PM: Registered nosave memory: [mem 0x000a0000-0x000effff]
[    0.000000] PM: Registered nosave memory: [mem 0x000f0000-0x000fffff]
[    0.000000] Memory: 1837732k/2097124k available (7324k kernel code, 392k absent, 259000k reserved, 6305k data, 1832k init)
[    0.000000] please try 'cgroup_disable=memory' option if you don't want memory cgroups
[    0.541105] Initializing cgroup subsys memory
[    1.640271] Freeing initrd memory: 41740k freed
[    1.840650] Non-volatile memory driver v1.3
[    1.846069] crash memory driver: version 1.1
[    2.033498] Freeing unused kernel memory: 1832k freed
[    2.041958] Freeing unused kernel memory: 856k freed
[    2.045739] Freeing unused kernel memory: 684k freed
[    4.926318] [TTM] Zone  kernel: Available graphics memory: 941434 kiB
[    4.935019] [drm] qxl: 16M of VRAM memory size
[    4.937533] [drm] qxl: 63M of IO pages memory ready (VRAM domain)
[    4.940743] [drm] qxl: 16M of Surface memory size
```

Như mình đã nói, số ram hệ điều hành Linux đã nhận thực sự là 2047 MB (`System RAM: 2047MB`). Nhưng để ý dòng (`Memory: 1837732k/2097124k available (7324k kernel code, 392k absent, 259000k reserved, 6305k data, 1832k init)`), `Kernel` hệ điều hành đã tự cấp phát một lượng ram nhỏ để xử lý một số tác vụ trong quá trình khởi động OS, số ram này sẽ được giải phóng một phần. Tức tuy tổng dung lượng ram là 1.838 GB nhưng thực tế bạn có thể sử dụng nhiều hơn thế.

## Nguồn

https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/filesystems/proc.txt

https://serverfault.com/questions/219987/why-doesnt-the-value-in-proc-meminfo-seem-to-map-exactly-to-the-system-ram/219990#219990

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>
