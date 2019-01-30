---
date: 2019-01-23
title: "High Availability - Phần 3: Tổng quan về Quorum Corosync, STONITH/Fencing"
categories:
  - Linux
description: Tổng quan về Quorum Corosync, STONITH/Fencing
author: thanhnb
tags: [Cluster, Load balancer, High Availability, Linux, CentOS7]
type: Document
---

## Tổng quan về Quorum
### Định nghĩa
`split-brain` là hiện tượng cluster lớn bị tách ra thành nhiều cluster nhỏ. Điều này sẽ dẫn đến sự mất đồng bộ giữa các tài nguyên,ảnh hướng tới sự toàn vẹn của hệ thống. 

Quorum là giải pháp ngăn chặn hiện tượng "split brain" trong cluster. Cluster có quorum chỉ khi số node đang hoạt động nhiều hơn một nửa số node thuộc Cluster ((Số node hoạt động) > (tổng số node của cụm) / 2).

Quorum được thiết lập bằng cơ chế `voting`. Khi node thuộc cluster xảy ra sự cố hoặc mất kết nối với phần còn lại của cluster, các node đang hoạt động sẽ `vote` cho việc node nào sẽ bị đóng băng cô lập, node nào sẽ tiếp tục hoạt động.

Kỹ thuật Quorm được hỗ trợ mặc định trong pacemaker, với 2 kỹ thuật:
- Hỗ trợ kỹ thuật `Resource-driven cluster` - Kỹ thuật phân cấp, nhóm tài nguyên để quản lý độc lập
- Hỗ trợ kỹ thuật `Quorate Clusters` - Kỹ thuật tính điểm của các node thuộc cluster, ý tưởng của kỹ thuật là khi cụm lớn bị phân mảnh thành 2 phần, cluster sẽ đánh giá so sánh số điểm của 2 cụm để quyết định cụm nào sẽ tiếp tục chạy, cụm nào sẽ bị đóng băng hoặc tắt hẳn. 
- Công thức tính quorum (tức số node tối thiểu để cụm hoạt động bình thường)
  ```
  (Số node hoạt động) > (tổng số node của cụm) / 2
  ```

### Ví dụ về quorum:
Đối với Cluster gồm 2 node, tổng số vote là 2. Dựa theo kỹ thuật `voting` quorum sẽ chỉ hoạt động nếu số vote lớn hơn 1 nửa số node hoạt động (tức lớn hơn 1 node). Vì vậy nếu có 1 node xảy ra sự cố, cả cluster sẽ dừng hoạt động

![](/images/img-tong-quan-ve-quorum-stonith-fencing/pic1.png)

Đối với Cluster gồm 3 node, tổng số vote là 3. Dựa theo kỹ thuật `voting` quorum sẽ chỉ hoạt động nếu số vote lớn hơn 1 nửa số node hoạt động (tức lớn hơn 1 node). Vì vậy nếu có hơn 2 node xảy ra sự cố, cả cluster sẽ dừng hoạt động


![](/images/img-tong-quan-ve-quorum-stonith-fencing/pic2.png)


Trong trường hợp cluster gồm 6 node bị phân mảnh thì cần ít nhất 4 node cùng hoạt động trong cluster để hình thành quorum. Trong trường hợp bị phân mảnh nhỏ hơn pacemaker sẽ cô lập hoặc ngừng cung cấp dịch vụ.

![](/images/img-tong-quan-ve-quorum-stonith-fencing/pic3.png)

Các tùy chọn khi pacemaker mất Quorum (Số node hiện có không thể tạo thành quorum):
- ignore: Tiếp tục quản trị duy trì hoạt đông cluster kể cả khi mất quorum
- freeze: Tiếp tục quản trị duy trì hoạt đông cluster nhưng đóng băng tài nguyên xảy ra sự cố, không cố gắng khôi phục
- stop: Ngừng cung cấp dịch vụ cluster khi mất quorum
- suicide: Cô lập các node đang xảy ra sự cố


## Tổng quan về STONITH/Fencing
### Định nghĩa
STONITH viết tắt `Shoot-The-Other-Node-In-The-Head`, kỹ thuật bảo vễ dữ liệu khỏi các node xảy ra sự cố. Trong trường hợp node không phản hổi cluster nhưng không chắc chắn trạng thái của node, các dịch vụ trên node, cách tốt nhất để bảo đảm dữ liệu là tắt nóng, bảm đảm node thực sự offline. STONITH sẽ sử dụng các giao thực quản trị từ xa hỗ trợ bởi phần cứng, cho phép cluster thao tác trực tiếp với server vật lý.

Trong hệ thống cluster, các node chia sẻ tài nguyên sử dụng, nếu một node xảy ra sự cố tài nguyên chia sẻ giữa các node có thể bị ảnh hướng dẫn đến trạng thái các node khác cũng trở nên thất thường. Để tránh hiện tượng này xảy ra, ta sẽ sử dụng kỹ thuật fencing để cô lập các tài nguyên của node đang xảy ra sự cố.

Fencing sẽ loại bỏ kết nối giữa node tới các tài nguyên chia sẻ giữa cluster (shared storage, database, ..). Cluster thực hiện cô lập tài nguyên (`fencing`) thông qua kỹ thuật `STONITH`.

Với pacemaker, khi nhận thấy node xảy ra sự cố, nó sẽ thông báo cho các node đang hoạt động về node lỗi và cô lập node thông qua `STONITH`.

Cô lập node thông `STONITH` có thể được thực hiện thông qua nhiều mức, dựa trên nhiều loại thiết bị hỗ trợ
- Uninterruptible Power Supply (UPS): Cô lập tài nguyên cung cấp năng lượng bằng bộ lưu điện, sử dụng khi hệ thống cung cấp năng lượng xảy ra sự cố
- Power Distribution Unit (PDU): Cô lập tài nguyên cung cấp năng lượng bằng thiết bị cấp phát nguồn, sử dụng khi hệ thống cung cấp năng lượng xảy ra sự cố
- Blade power control device: Hệ thống chuyên dụng trong các datacenter, sử dụng để cô lập các cluster node xảy ra sự cố.
- Lights-out device: Thiết bị gắn mạng hỗ trợ giao thức quản trị từ xa có phép cố lập tài nguyên dựa trên thao tác tắt bật. VD: HP Integrated Lights-Out - (HP ILO); Dell Remote Access Controller (DRAC)

Các giao thức hỗ trợ STONITH:
- IPMI - intelligent Platform Management Interface ( General Standard )
- IDRAC - Integrated Dell Remote Access ( Dell )
- ILO - Integrated Lights-Out ( HP )
- IMM - Integrated Management Module ( IBM )

Ngoài ra, nếu dịch vụ chạy trên các máy ảo, các vật chủ hỗ trợ phương pháp giảm trị:
- fence_vmware_soap (vSphere Vmware)
- Fence virtd, fence-agents-virsh  (Cho trình ảo hóa hỗ trợ lib virt

Ví dụ minh họa:
Trong trường hợp Node 3 xảy ra sự cố, cluster hình thành giữa node 1 và node 2 sẽ tắt nóng node 3 thông qua cấu hình STONITH (ILO, IDRAC, ...)

![](/images/img-tong-quan-ve-quorum-stonith-fencing/pic4.png)

## Nguồn

https://github.com/hocchudong/ghichep-pacemaker-corosync

https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/ch-operation-haao#s1-quorumoverview-HAAO

https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/s1-fencing-haao

---
Thực hiện bởi <a href="https://cloud365.vn/" target="_blank">cloud365.vn</a>