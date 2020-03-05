# Keepalive
- Đầu tiên ta phải xác định rằng keepalive ở đâu là một hình thức kỹ thuật chứ không phải một giao thức.
- Như tên gọi thì keepalive là giữ thêm thời gian sống connection, kiểm tra đường tuyền kết nối giữa hai thiết bị, server sẽ đóng TCP connection sau khi đã return response, điều này sẽ làm cho hiệu suất transaction giảm xuống, trải nghiệm người dùng sẽ bị ảnh hưởng ít nhiều, lượng RAM và CPU trên server sẽ bị tiêu tốn rất nhiều.
- Để giải quyết vấn đề này, một giải pháp hữu hiệu đã được đưa ra đó là **keepalive**, nó được sử dụng rất phổ biến hiện nay
vì tầm quan trọng và mức độ giải quyết vấn đề. Nó sẽ giữ thời gian sống của connection trong khoảng thời gian được thiết lập, cóp nghĩa là thay vì mỗi request một connection như trước thì nếu sử dụng keepalive thì chỉ cần một connection sẽ có nhiều request được chuyển đi.
- Có hai thông số cần lưu ý là:
  + **Keepalive interval**: thời gian gửi giữa các gói tin
  + **Keepalive retries**: số lần kết nối lại sau thời gian timeout
- Khi không nhận được reply thì data tiếp theo sẽ được route đến một đường dẫn khác hoặc chờ trong thời gian timeout cho đến khi đường truyền được ổn định trở lại => tạo tính ổn định cho cấu trúc mạng kết nối.
- Chúng ta sẽ đi tìm hiểu hai giao thức sử dụng keepalive đó là TCP keepalive và HTTP keepalive
## TCP keepalive 
- TCP là giao thức hướng luồng, phải thiết lập kết nối trước khi dữ liệu được truyền đi, khi bạn gửi một packet thăm dò với no-data, và nó được gắn môt ACK flag, nếu như bạn nhận được phản hồi từ bên kia, có nghĩa là đường truyền đang ổn định, nếu bạn không nhận được reply thì đường truyền đang gặp vấn đề, nó có thể là bị reboot, mất điện, mất mạng,...
- Khi hai host kết nối với nhau qua TCP/IP thì TCP keepalive được dùng để xác định xem đường truyền có đủ khả năng để vận chuyện gói tin hay không.
- Nếu sau khi hai host đã thiết lập được kết nối, một bên bắt đầu gửi data, nhưng đường truyền có vấn đề thì sao?
  + Nếu đường truyền ổn định lại sau đó mà vẫn đang trong thời gian timeout thì vẫn có thể gửi data đi.
  + Nhưng nếu hết timeout thì hệ thống sẽ phải thiết lập kết nối mới.
- <img src="https://cuongquach.com/resources/images/2017/08/3-buoc.png">
    + ACK=101 có nghĩa là nó đã nhận được byte thừ 0 đến 100 rồi, nó gửi ACK này để mong muốn nhận được một byte thứ 101.
    + SEQ=101 là để đáp ứng lại mong muốn, và ACK=301 là để báo đã nhận được SEQ=300.
- Thay vì gửi từng byte như trước đây thì bây giờ sẽ dựa vào windows size để gửi
  + <img src="https://cuongquach.com/resources/images/2017/08/fixed.png">
  + Sau khi gửi hết số lượng windows size thì nó gắn cờ ACK=+1 để yêu cầu thêm
  + Nhưng nếu windows size nào mà bị thất lạc thì bên nhận sẽ gửi ACK của byte đó đến bên nhận để mong muốn nhận lại.
- Nhưng khi Sender quá nhiều thì phải làm thế nào?
  + <img src="https://cuongquach.com/resources/images/2017/08/flow.png">
  + Nó sẽ chuyển vào bộ nhớ đệm của hệ thống đồng thời nó sẽ gửi thông báo đến nới gửi là đừng có gửi nữa, sau khi xử lý xong 
các gói tin đang chờ trong buffer thì thông báo đến cho nơi gửi là có thể nhận được dữ liệu rồi nên cứ gửi đi.

- Sử dụng wireshark để bắt gói tin được gửi từ client đến server:
  + <img src="https://i.imgur.com/qgKTgeN.png">
