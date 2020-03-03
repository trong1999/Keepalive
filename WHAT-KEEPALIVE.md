# Keepalive
- Như tên gọi thì keepalive là giữ thêm thời gian sống connection, như chế độ default thì sau mỗi connection thì nó sẽ 
close connection, server sẽ đóng TCP connection sau khi đã return response, điều này sẽ làm cho hiệu suất transaction giảm xuống,
trải nghiệm người dùng sẽ bị ảnh hưởng ít nhiều, lượng RAM và CPU trên server sẽ bị tiêu tốn rất nhiều.
- Để giải quyết vấn đề này, một giải pháp hữu hiệu đã được đưa ra đó là **keepalive**, nó được sử dụng rất phổ biến hiện nay
vì tầm quan trọng và mức độ giải quyết v
