# Thiết Kế Không Gian Đa Profile Và Kiến Trúc Bookmark Cho Trạm Khám Bệnh

## ***Phần 1: Báo cáo phân tích kiến trúc đa Profile***

***1. Phân tích nguyên nhân sâu xa dẫn đến hiện tượng 'văng phiên đăng nhập' (Session Conflict)***

- Cơ chế lưu trữ của Profile mặc định: Khi nhiều người dùng chung một cửa sổ trình duyệt (Default Profile), tất cả dữ liệu phiên làm việc, Session Cookies và Local Storage đều được ghi chung vào một thư mục gốc User Data duy nhất trên ổ cứng.

- Xung đột Cookie định danh: Trình duyệt phân biệt phiên đăng nhập dựa trên các tệp Cookie lưu trong thư mục này. Khi Bác sĩ trực đang đăng nhập hệ thống CarePulse, nếu thực tập sinh mở thêm tab đăng nhập tài khoản khác (hoặc nếu hệ thống sử dụng chung biến phiên/token quá hạn), các yêu cầu HTTP gửi lên máy chủ sẽ mang thông tin ghi đè hoặc bị hệ thống bảo mật của CarePulse định dạng là "bất thường/trùng lặp phiên", dẫn đến việc kích hoạt cơ chế bảo mật tự động đăng xuất (Logout) tài khoản cũ để bảo vệ dữ liệu.

***2. Thiết kế Mô hình 3 Profile chuyên dụng cho máy tính trạm khám CarePulse***

Để cách ly dữ liệu hoàn toàn, ta chia máy tính trạm thành 3 Profile độc lập (mỗi profile sở hữu một không gian thư mục riêng trong User Data):

- Profile 1: [WORK] CarePulse_Official

   - Mục đích sử dụng: Dành riêng cho Bác sĩ trực và Điều dưỡng viên thực hiện nghiệp vụ khám chữa bệnh, kê đơn trên hệ thống CarePulse.

   - Màu Theme nhận diện: Xanh y tế (Medical Blue) để nhận biết ngay lập tức đây là môi trường công việc chính thức.

   - Dữ liệu độc lập:   Lưu Cookies BHYT/CarePulse, Lịch sử tra cứu y khoa, Bookmark chuyên ngành, không chứa bất kỳ tài khoản cá nhân nào.

- Profile 2: [PERSONAL] Personal_Account

  - Mục đích sử dụng: Dành cho cá nhân nhân viên y tế tra cứu thông tin riêng, giải trí giờ nghỉ (Youtube, Facebook, Email cá nhân).

  - Màu Theme nhận diện: Cam ấm hoặc Xám trung tính.

  - Dữ liệu độc lập: Cookies giải trí, lịch sử mua sắm, mật khẩu cá nhân được mã hóa riêng biệt.    

## ***Phần 2: Thiết kế hệ thống Bookmark & Quy trình an toàn***

***3. Dựng cây thư mục Bookmark chuẩn (Ctrl + Shift + O)***

Để tối ưu tốc độ tìm kiếm dưới 5 giây cho mỗi ca khám, tổ chức thanh Bookmarks Bar thành 3 thư mục chính:

-  01_CarePulse_Clinic

   - Trang chủ hệ thống CarePulse (Phần mềm quản lý khám chữa bệnh chính).

   - Tra cứu thông tin Bệnh nhân ngoại trú / Lịch hẹn trực.

   - Báo cáo thống kê y tế hàng ngày.


- 02_Pharma_Portal  

   - Cổng thông tin Tra cứu Dược thư Quốc gia Việt Nam.

   - Danh mục thuốc đấu thầu bảo hiểm y tế (BHYT).

   - Cổng tra cứu tương tác thuốc (Drug Interactions).

- 03_Medical_Tools

   - Cổng tra cứu Giám định BHYT trực tuyến.

   - Google Dịch (Hỗ trợ đọc tài liệu y khoa quốc tế).

   - Hệ thống tra cứu mã ICD-10 (Bệnh tật quốc tế).

***4. Hướng dẫn áp dụng mẹo Favicon-Only tiết kiệm không gian***

Để thanh Bookmark chứa hàng chục trang web mà không bao giờ bị tràn hay xuống dòng:

- Nhấp chuột phải vào từng Bookmark trên thanh công cụ $\rightarrow$

- Tại ô Name (Tên): Xóa toàn bộ chữ, chỉ để lại khoảng trống hoặc ký hiệu icon nhỏ (ví dụ giữ lại icon gốc của trang web).

- Lợi ích: Trình duyệt Chrome sẽ tự động co gọn các ô bookmark lại chỉ bằng biểu tượng Favicon hình vuông nhỏ gọn, giúp tiết kiệm diện tích gấp 3 lần, cho phép hiển thị hơn 30–40 liên kết ngay trên một hàng mà không cần cuộn chuột.

***5. Xử lý Bẫy an ninh: Tình huống đoàn thanh tra hoặc khách mượn máy 30 phút***

- Lựa chọn chế độ duyệt web: Khi có khách hoặc đoàn kiểm tra mượn máy trong thời gian ngắn, nhân viên phòng khám bắt buộc phải sử dụng Guest Profile (Chế độ Khách) hoặc mở Incognito Window (Cửa sổ ẩn danh).

  - Khuyên dùng: Guest Profile là lựa chọn tối ưu nhất cho máy tính dùng chung vì nó tạo ra một phiên làm việc hoàn toàn sạch sẽ, không lưu lịch sử, không đọc được dữ liệu của bất kỳ Profile cá nhân nào trên máy và tự động xóa sạch toàn bộ tệp tạm, cookie ngay khi đóng cửa sổ.

- Cảnh báo nguy cơ nghiêm trọng khi bật tính năng Cloud Sync: Tuyệt đối KHÔNG đăng nhập tài khoản Google cá nhân để bật tính năng Cloud Sync (Đồng bộ hóa) trên máy tính y tế dùng chung này. Nếu bật Sync, toàn bộ mật khẩu đã lưu, lịch sử tìm kiếm cá nhân, hình ảnh và dữ liệu nhạy cảm của bạn sẽ tự động đồng bộ về máy trạm công cộng này, đồng thời đẩy dữ liệu rác lên tài khoản cá nhân của bạn, gây nguy cơ rò rỉ thông tin cực kỳ cao sau khi bạn rời đi.  