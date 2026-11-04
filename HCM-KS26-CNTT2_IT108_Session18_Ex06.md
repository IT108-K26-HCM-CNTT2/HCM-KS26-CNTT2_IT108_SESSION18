

## Phần 1. Phân tích nguyên nhân web chậm

Dùng tab Network trên DevTools (đã tích chọn **Disable Cache**) để kiểm tra trang `portal.greenlog.vn`, em nhận thấy trang web bị chậm 20s do các lý do sau:

1. **Lỗi đứt link (Status 404):** Khi load trang có một số request bị tô đỏ (báo lỗi 404 Not Found). Do trình duyệt không tìm thấy file nên phải chờ mất thời gian timeout, làm gián đoạn việc tải trang.
2. **Ảnh bản đồ quá nặng:** Trên biểu đồ Waterfall, thanh **Content Download** của các file ảnh bản đồ kéo rất dài. Do ảnh chưa qua xử lý hay nén dung lượng nên tốn nhiều băng thông để tải về.
3. **Chưa tối ưu file tĩnh:** Trình duyệt phải chờ các file CSS và JS quan trọng tải xong hoàn toàn mới vẽ được giao diện (Render), làm màn hình bị trắng lâu.

---

## Phần 2. Đề xuất 3 bước xử lý

Để khắc phục lỗi và làm web chạy nhanh hơn, em đề xuất 3 bước xử lý như sau:

*   **Bước 1: Sửa lại các đường dẫn bị lỗi 404**
    *   Kiểm tra các request bị báo đỏ trong tab Network, xem lại đường dẫn file script hoặc ảnh xem bị sai ở đâu.
    *   Sửa lại đúng URL trên server hoặc xóa các link file không còn dùng đến trong mã nguồn HTML.
*   **Bước 2: Nén và chỉnh lại kích thước hình ảnh**
    *   Đổi các ảnh bản đồ lộ trình sang định dạng nhẹ hơn như `.webp`.
    *   Giảm dung lượng ảnh xuống mức vừa đủ nhìn và thêm thuộc tính `loading="lazy"` để ảnh ở dưới cùng khi nào cuộn tới mới tải.
*   **Bước 3: Bật Cache cho trình duyệt (Browser Cache)**
    *   Cấu hình Server (Nginx hoặc Apache) thêm header `Cache-Control` cho các file tĩnh (CSS, JS, Ảnh).
    *   Việc này giúp trình duyệt lưu lại các file cố định để lần sau truy cập không cần tải lại từ đầu.

---

## Phần 3. Giải thích mã status 304 Not Modified

Khi bấm F5 tải lại trang mà thấy trình duyệt trả về mã **304 Not Modified**, đây là một tín hiệu rất tốt vì:

*   Mã 304 có nghĩa là file đó ở trên máy của mình (local cache) vẫn giống hệt file trên máy chủ, không có gì thay đổi.
*   Máy chủ chỉ gửi về đúng cái mã thông báo chứ **không gửi lại toàn bộ file** đó nữa.
*   Nhờ vậy, dung lượng dữ liệu truyền qua mạng cực kỳ ít (chỉ tốn vài byte header), trang web hiện ra gần như ngay lập tức và tiết kiệm băng thông cho cả máy khách lẫn máy chủ.