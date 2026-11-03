# Sự cố không truy cập được hệ thống quản trị

## ***Phần 1. Phân tích tiến trình DNS Lookup & Hậu quả sai lệch tên miền***

***Xác định Input và Output***

- **Input:** Tên miền dạng chữ do người dùng nhập vào thanh địa chỉ trình duyệt, cụ thể là phong-dieu-phoi.greenlog.vn (hoặc bản gõ nhầm của Nam)

- **Output:** Địa chỉ IP dạng số của máy chủ (ví dụ: 192.168.1.15) để máy tính định tuyến gói tin mạng đến đúng đích

***Sơ đồ luồng dữ liệu DNS Lookup:***

- Browser Cache: Trình duyệt kiểm tra xem đã lưu IP của tên miền này trong bộ nhớ tạm gần đây chưa

- OS Cache / Hosts File: Nếu không có, trình duyệt hỏi hệ điều hành (Windows/Linux) xem bộ nhớ đệm OS hoặc file hosts có lưu thông tin không

- Root & TLD Servers: Yêu cầu được chuyển qua các máy chủ cấp cao hơn (.vn) để tìm đến máy chủ quản lý danh tính chính thức (Authoritative DNS Server) của miền greenlog.vn.

***Hậu quả của việc gõ nhầm tên miền (phong-dieu-phoi.grenlog.vn - thiếu chữ 'e'):***

- Giống như việc tra cứu nhầm tên trong danh bạ điện thoại, khi hệ thống phân giải tên miền tra cứu chuỗi ký tự bị sai chính tả này, máy chủ quản lý tên miền cấp cao sẽ trả về kết quả không tồn tại.

- Trình duyệt nhận mã lỗi DNS_PROBE_FINISHED_NXDOMAIN (Non-Existent Domain). Điều này khẳng định nhận định của Nam rằng "máy chủ công ty bị sập nguồn" là sai hoàn toàn — vấn đề nằm ở phía client (gõ sai URL), yêu cầu hoàn toàn chưa hề rời khỏi mạng nội bộ để đến được máy chủ.

## ***Phần 2. Triển khai: Chẩn đoán lỗi mạng & HTTP Status Code***

***Phân biệt bản chất lỗi DNS và lỗi 500 Internal Server Error:***

- Lỗi DNS (NXDOMAIN): Xảy ra ở bước phân giải tên miền (tầng ứng dụng/giao mạng ban đầu). Yêu cầu chưa hề chạm được vào máy chủ.

- Lỗi 500 Internal Server Error: Đây là mã trạng thái HTTP. Lỗi này chỉ xuất hiện khi yêu cầu đã vượt qua bước DNS, kết nối thành công và chạm tới máy chủ, nhưng mã nguồn ứng dụng web bên trong server bị lỗi ngoại lệ (exception), lỗi cú pháp code, hoặc cấu hình sai.

***Quy trình kiểm tra xem yêu cầu đã chạm tới máy chủ hay chưa:***

- Kiểm tra lại chính tả của đường dẫn URL trên thanh địa chỉ.

- Mở cửa sổ dòng lệnh (Terminal/PowerShell), dùng lệnh ping hoặc nslookup để kiểm tra xem tên miền có phân giải ra IP thành công hay không.

- Kiểm tra Tab Network trong Developer Tools (F12) của trình duyệt để đọc mã trạng thái HTTP Status Code trả về (Nhóm 2xx là thành công, nhóm 4xx là lỗi phía client, nhóm 5xx là lỗi phía server).

## ***Phần 3. Kiểm thử: Xử lý trường hợp mã 404 Not Found***

***Giả định Nam đã sửa lại đúng chính tả tên miền phong-dieu-phoi.greenlog.vn nhưng trình duyệt lại trả về mã 404 Not Found:***

**Vấn đề hệ thống đang gặp phải:***

- Quá trình phân giải DNS đã hoàn tất tốt đẹp và yêu cầu HTTP đã chạm tới máy chủ thành công (vì mã 404 là do chính máy chủ web phản hồi lại cho client).

- Tuy nhiên, đường dẫn cụ thể (path/URI) mà Nam đang truy cập không tồn tại trên hệ thống tệp hoặc cấu hình routing của máy chủ web (có thể đường dẫn đúng phải là /admin/phong-dieu-phoi hoặc trang quản trị đã bị di dời sang một thư mục/endpoint khác).