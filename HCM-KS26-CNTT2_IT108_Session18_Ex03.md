# Xử lý lỗi bố cục giao diện hệ thống theo dõi xe

## ***Phần 1. Phân tích: Sơ đồ Box Model hiện tại***

**Dựa trên thông tin soi từ tab Computed của phần tử chứa khung "Thông tin tài xế" (div.it...), cấu trúc 4 lớp của CSS Box Model đang gặp hiện tượng:**

- Margin: Đang bị đẩy lệch (ví dụ margin: -34px hoặc các giá trị âm) khiến phần tử bị kéo dịch sang vị trí của khung khác.   

- Border: Khung viền bao quanh phần tử.

- Padding: Khoảng đệm bên trong.

- Content: Vùng chứa nội dung văn bản và thông tin tài xế bên trong cùng.

## ***Phần 2. Triển khai: Thông số CSS mới tại tab Styles***

Để khắc phục tình trạng đè lấp giao diện mà không chạm vào mã nguồn gốc, bạn hãy click vào phần tử .it... (Thông tin tài xế) trong bảng Elements và chỉnh sửa trực tiếp các thông số tại tab Styles như sau:

***CSS: div.it... {
    width: 100%;             /* Đưa về chuẩn 100% thay vì 110% hay 140% */
    margin: 0px 0px 15px 0px;/* Loại bỏ margin âm, tạo khoảng cách phía dưới */
    padding: 15px;           /* Giữ khoảng đệm vừa vặn */
    box-sizing: border-box;  /* Gom padding và border vào trong width */
}

***(Có sử dụng chat gpt để làm phần này)***

## ***Phần 3. Kiểm thử: Xử lý trường hợp thay đổi Margin nhưng phần tử không di chuyển***

Nếu bạn đã chỉnh sửa Margin nhưng phần tử vẫn đứng im, hãy kiểm tra các nguyên nhân sau tại tầng Browser Engine:

- Bị ghi đè bởi cờ !important: Kiểm tra xem trong bảng Styles có dòng nào khác gắn cờ !important đang ép giá trị cũ hay không.

- Thuộc tính position: Nếu phần tử đang đặt position: absolute hoặc position: fixed, các thuộc tính margin theo dạng luồng thông thường (static/relative) có thể không tác dụng như ý mà phải chỉnh qua top, left, right, hoặc bottom.