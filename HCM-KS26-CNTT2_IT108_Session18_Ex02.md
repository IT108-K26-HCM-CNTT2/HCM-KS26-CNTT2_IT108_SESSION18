#  Sự cố phiên làm việc trên hệ thống vận tải

## ***Phần 1. Phân tích: Sự đánh đổi giữa Cache (MB/GB) và Cookies (~4KB)***

***Cache (Dung lượng lớn: MB đến GB):***

- Mục đích: Lưu trữ các tệp tĩnh của trang web (hình ảnh, tệp CSS, JavaScript, font chữ) để lần truy cập sau trình duyệt không phải tải lại từ server, giúp tăng tốc độ hiển thị.

- Đánh đổi: Chiếm dụng nhiều không gian lưu trữ của thiết bị; nếu không được xóa/làm mới kịp thời, người dùng sẽ thấy các giao diện cũ (như trường hợp màu sắc cũ bị kẹt lại).

***Cookies (Dung lượng nhỏ: tối đa ~4KB):***

- Mục đích: Lưu trữ các thông tin định danh nhỏ gọn (như Token xác thực phiên đăng nhập, ID người dùng) được gửi kèm tự động trong mỗi HTTP Request lên server.

- Đánh đổi: Dung lượng cực kỳ hạn chế (chỉ khoảng 4KB), không phù hợp để chứa dữ liệu lớn như giỏ hàng cồng kềnh hay danh sách sản phẩm chi tiết (dữ liệu giỏ hàng thường nên lưu ở Server-side kết hợp với Database hoặc Client-side như localStorage/SessionStorage).

## ***Phần 2. Triển khai: Cấu trúc mẩu tin Cookie bảo mật***

Để khắc phục tình trạng shipper bị văng trạng thái đăng nhập khi đóng trình duyệt, ta chuyển từ Session Cookie sang Persistent Cookie bằng cách định nghĩa thời gian sống (Max-Age), đồng thời siết chặt bảo mật chống đánh cắp phiên (Session Hijacking):

**HTTP: Set-Cookie: shipper_token=eyJhbGciOiJIUzI1Ni...; Max-Age=604800; Path=/; Secure; HttpOnly; SameSite=Strict**

## ***Phần 3. Kiểm thử: Thao tác Hard Reload để xóa Cache đệm***

Nếu shipper đã được cập nhật giao diện mới (màu sắc mới) nhưng trình duyệt vẫn hiển thị màu cũ do bị kẹt bộ nhớ đệm (Cache), hãy hướng dẫn họ thực hiện thao tác Hard Reload (tải lại mạnh) để ép trình duyệt bỏ qua dữ liệu cũ và tải lại hoàn toàn từ máy chủ:

- Trên máy tính (Windows/Linux): Nhấn tổ hợp phím Ctrl + Shift + R hoặc Ctrl + F5.

- Trên thiết bị di động / Trình duyệt di động: Vào cài đặt trình duyệt chọn Xóa dữ liệu duyệt web (Clear browsing data) hoặc thực hiện tải lại trang khi đã bật chế độ làm mới bộ nhớ cache.