---
title: Tìm hiểu mô hình mạng NAT trong KVM
categories:
  - Linux
description: Tìm hiểu mô hình mạng NAT trong KVM
author: niemdt
tags: [KVM, Linux, CentOS]
type: Document
---
## Tổng quan

KVM được biết đến là một cơ sở hạ tầng ảo hóa cho nhân Linux. KVM cũng cung cấp các mô hình mạng trong việc ảo hóa network. Các mô hình bao gồm:

 * NAT
 * Host-only
 * Linux-bridge

Ở <a href="https://blog.cloud365.vn/linux/huong-dan-tao-su-dung-mo-hinh-mang-linux-bridge-KVM/" target="_blank">bài trước</a> tôi đã giới thiệu về mô hình mạng linux-bridge. Ở bài viết này tôi sẽ giới thiệu về mô hình NAT trên KVM.

## Chuẩn bị

Một máy chạy hệ điều hành CentOS 7 hoặc Ubuntu cài đặt KVM có một card mạng kết nối với internet và cài đặt một VM bên trong máy đó.

*Chú ý* máy vật lý ở đây là máy cài KVM. Máy này có thể là một máy ảo nhưng ở đây ta coi nó như một server vật lý.

## Mô hình 

![](/images/img-nat-kvm/a1.png)

Nếu như với mô hình linux bridge KVM tạo ra một virtual switch thì ta cũng có thể hình dung với mô hình mạng NAT này KVM sẽ tạo ra một thiết bị là virtual router. Khi ta tạo một dải mạng với mô hình NAT thì lúc này virtual router sẽ NAT từ dải mạng mà ta tạo ra ra địa chỉ của card mạng vật lý trên KVM host để đi ra ngoài internet.

Khi một dải mạng tạo ra ta sẽ thấy trên KVM host xuất hiện một thêm một card mạng. Card mạng này đóng vai trò là gateway cho dải mạng mà ta tạo ra.

![](/images/img-nat-kvm/1.png)

Để hiểu rõ hơn về mô hình này cũng như đường đi của gói tin từ VM ra ngoài internet tôi sẽ dùng lệnh `ping 8.8.8.8` sau đó bắt gói tin ở các vị trí mà gói tin có thể đi qua để ta có thể hình dung rõ cách hoạt động của nó. Ở đây tôi sẽ bắt tại các vị trí sau:

Trên card `eth0` của VM

![](/images/img-nat-kvm/14.png)

Trên card `virbr2` là gateway của mạng 

![](/images/img-nat-kvm/15.png)

Và trên card `enp0s25` của KVM host

![](/images/img-nat-kvm/13.png)

Như vậy ta có thể thấy như sau

![](/images/img-nat-kvm/6.png)

Như vậy VM có thể biết được địa chỉ ở bên ngoài internet nhưng các máy ở bên ngoài sẽ không thể thấy được VM. Như vậy địa chỉ của KVM host sẽ đại diện cho các VM khi giao tiếp với bên ngoài.

## Tạo mô hình NAT trên KVM

Ở đây tôi sử dụng công cụ `virt-manager` để tạo và quản lý mô hình mạng này.

Trước tiên bạn vào công cụ `virt-manager` trên thanh công cụ click vào `Edit` chọn `Connection Details` chọn `Virtual Networks` và click vào biểu tượng dấu cộng như bên dưới

![](/images/img-nat-kvm/7.png)

Đặt tên cho mạng bạn muốn tạo

![](/images/img-nat-kvm/8.png)

Chọn dải mạng và địa dải địa chỉ IP cấp phát cho VM nếu dùng cấp phát DHCP. Sau đó click `Forward`

![](/images/img-nat-kvm/9.png)

Nếu bạn muốn sử dụng cả IPv6 thì click chọn ở đây. Ở đây tôi không sử dụng đến nó nên tôi không chọn

![](/images/img-nat-kvm/10.png)

Chỉ ra card mạng mà bạn muốn dùng nó để gắn vào virtual router để làm đường đi ra internet

![](/images/img-nat-kvm/11a.png)

Vào trong VM kết nối đến mạng này sau đó reboot lại VM

![](/images/img-nat-kvm/3.png)

Ta có thể kiểm tra VM đã nhận đúng dải mạng

![](/images/img-nat-kvm/4.png)

Ping thử để kiểm tra xem mạng có hoạt động

![](/images/img-nat-kvm/12.png)

## Tổng kết

Bài viết có thể giúp bạn hiểu thêm về mô hình mạng NAT trong KVM. Trong bài viết chắc chắn còn nhiều thiếu sót rất mong được sự góp ý của các bạn.

Chúc bạn thành công!

Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>