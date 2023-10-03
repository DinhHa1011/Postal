- Đây là một list tính năng của Postal có thể thực hiện
### General features
- Support nhiều tổ chức với mail server và người dùng
- Đồ thị và số liệu thống kê hiển thị khối lượng thư đến và đi
- Truy cập tới để xem lịch sử messages
- Truy cập tới để xem tất cả outgoing and incoming messages queue
- Set up webhooks để nhận thông tin live về thông tin gửi trong thời gian thực. Tất cả truy cập vào 7 ngày trước của yêu cầu webhook cũng được lưu trữ cho mục đích gỡ lỗi
- Built-in DNS check và mornitor để đảm bảo domain bạn gửi mail được định cấu hình chính xác để có khả năng gửi tối đa
- Cấu hình lưu trữ trên mỗi server để đặt thời lượng message sẽ được lưu trong cơ sở dữ liệu và kích thước tối đa để lưu trên đĩa
- Ghi nhật ký hoàn chỉnh để có thể dễ dàng xác định được vấn đề gửi thư
- Mail server wide search tools để tìm thư cần điều tra
### Outgoing e-mails
- Gửi message tới SMTP server hoặc sử dụng HTTP API
- Quản lý nhiều thông tin đăng nhập trên server
- Support DKIM signing cho outbound messages
- Cho phép phát triển để giữ message qua Postal mà không thực sự chuyển chúng đến người nhận (message có thể được xem trong giao diện Postal)
- Danh sách chặn được tích hợp sẵn để tránh gửi mail đến người nhận không tồn tại hoặc không thể chấp nhận email
- Click và mở tracking để theo dõi thời điểm người nhận mở email và click vào các link trong đó
- Config mỗi server gửi limit để tránh lạm dụng trên mail server
- Quản lý nhiều pool của địa chỉ IP đang gửi
- Định cấu hình những người gửi hoặc người nhận khác nhau để gửi mail từ các địa chỉ IP nhất định
- Gắn tag mail để chắc chắn email có thể được cấp một tag để cho phép chúng được nhóm lại khi cần. Ví dụ, bạn có thể gắn tag `receipts` hoặc `password-reset` email như vậy
### Incoming e-mails
- Khả năng forward incoming e-mail tới HTTP endpoints
- Khả năng forward incoming e-mail tới SMTP server khác
- Khả năng forward incoming e-mail tới địa chỉ e-mail khác
- Spam & thread checking với SpamAssassin và ClamAV với ngưỡng config và trường khác để xử lý spam message
- 
