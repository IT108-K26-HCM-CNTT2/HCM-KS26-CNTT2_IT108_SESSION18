# Sự cố xung đột phiên đăng nhập trên trình duyệt

## ***Phần 1. Phân tích: Incognito Mode vs. Dedicated Profile***

***Chế độ ẩn danh***

- Cách lưu trữ: Lưu trữ trạng thái tạm thời hoàn toàn trên RAM và bộ nhớ đệm tạm của phiên làm việc.

- Đặc tính: Khi bạn đóng tất cả cửa sổ ẩn danh, toàn bộ cookie, lịch sử và dữ liệu phiên sẽ bị xóa sạch. Không phù hợp để lưu phiên đăng nhập lâu dài vì cứ mở lại là phải đăng nhập lại từ đầu.

***Hồ sơ chuyên dụng***

- Cách lưu trữ: Lưu trữ lâu dài, độc lập ngay trên ổ cứng (gồm thư mục chứa profile riêng cho từng tài khoản).

- Đặc tính: Cô lập hoàn toàn cookie, bộ nhớ cache, lịch sử duyệt web, mật khẩu và tiện ích mở rộng (extension) giữa các profile. Bạn có thể mở đồng thời 3 cửa sổ khác nhau cho 3 tài khoản mà không hề xảy ra xung đột phiên.

## ***Phần 2. Triển khai: Quy trình tạo Profile và Đồng bộ hóa Cloud Sync***

Để triệt tiêu rủi ro lộ dữ liệu cá nhân khi làm việc, hãy thiết lập mô hình 3 Profile độc lập (Personal - Work - Sandbox):

**1. Tạo Profile mới trên trình duyệt (Chrome/Edge):**

- Click vào ảnh đại diện ở góc trên bên phải trình duyệt $\rightarrow$

- Đăng nhập bằng tài khoản email tương ứng với từng môi trường (ví dụ: Email cá nhân cho profile Personal, Email công ty cho profile Work, Email test cho profile Sandbox).

**2. Kích hoạt Cloud Sync (Đồng bộ đám mây):**

- Trong mỗi Profile, bật tính năng Turn on sync (Bật đồng bộ hóa) gắn với tài khoản Google/Microsoft tương ứng của profile đó.

- Lợi ích: Toàn bộ Bookmark, mật khẩu đã lưu và tiện ích mở rộng sẽ được mã hóa và lưu trữ trên đám mây. Khi đổi sang máy tính mới, bạn chỉ cần đăng nhập lại tài khoản tương ứng là toàn bộ cây thư mục Bookmark công việc sẽ tự động đồng bộ xuống mà không sợ bị thất lạc hay trộn lẫn.

**Xây dựng cấu trúc cây thư mục Bookmark khoa học (cho Profile Work):**

- Công việc chính $\rightarrow$ Portal công ty, Hệ thống GreenLog.

- Phát triển $\rightarrow$ GitHub, GitLab, tài liệu API.

- Học tập $\rightarrow$ Canvas LMS, tài liệu nội bộ.

## ***Phần 3. Kiểm thử: Quy tắc sử dụng Guest Profile trên máy tính công cộng***

Khi phải làm việc trên một máy tính công cộng hoặc máy của người khác, tuyệt đối không dùng tài khoản cá nhân hay tài khoản công ty đăng nhập trực tiếp. Hãy tuân thủ quy tắc sau:

***Sử dụng chế độ Khách***

- Mở trình duyệt ở chế độ Guest Mode 

- Đặc tính bảo mật: Chế độ này tạo ra một không gian duyệt web hoàn toàn tạm thời, không lưu lại lịch sử duyệt web, không truy cập được vào dữ liệu của các profile khác trên máy đó, và tự động xóa sạch toàn bộ cookie/session ngay khi cửa sổ Guest được đóng lại, giúp bảo mật tuyệt đối thông tin định danh của bạn.