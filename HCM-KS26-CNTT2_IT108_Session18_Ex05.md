

## 1. PHÂN TÍCH KỸ THUẬT: VAI TRÒ CỦA JSON VIEWER

Trong hệ thống theo dõi hành trình xe thực tế, API từ thiết bị GPS trả về dữ liệu dạng thô (*raw JSON/minified*), không xuống dòng và không dấu cách nhằm tối ưu dung lượng truyền tải qua mạng di động (3G/4G/5G). 

Việc sử dụng công cụ **JSON Viewer** giải quyết 3 vấn đề chính trong quá trình gỡ lỗi (debugging):

1. **Cấu trúc hóa trực quan (Pretty Print & Syntax Highlighting):**  
   Tự động thụt lùi đầu dòng, phân chia màu sắc theo kiểu dữ liệu (`String`, `Number`, `Boolean`, `Null`). Giúp kỹ sư phân biệt nhanh giữa tọa độ độ kinh/vĩ (`float`) và trạng thái cảm biến (`boolean`).

2. **Kiểm tra nhanh DTO (Data Transfer Object):**  
   Thu gọn/mở rộng các node dữ liệu phức tạp (mảng chuỗi xe, thông tin tài xế, lịch sử di chuyển) mà không cần chép dữ liệu sang phần mềm bên thứ ba.

3. **Trích xuất JSON Path chính xác:**  
   Hỗ trợ sao chép đường dẫn trực tiếp tới key cần truy vấn (ví dụ: `data.vehicles[0].location.lat`), giảm thiểu lỗi typo khi viết code xử lý dữ liệu ở phía Client.

---

## 2. QUY TRÌNH TRIỂN KHAI & PHÂN QUYỀN

### 2.1. Danh sách 5 Extension thiết yếu

Để đảm bảo hiệu năng và tính an toàn, đội ngũ kỹ sư chỉ sử dụng đúng 5 tiện ích đã qua kiểm duyệt sau:

| STT | Tên Extension | Phân nhóm | Mục đích sử dụng tại GreenLogistics |
| :---: | :--- | :--- | :--- |
| **1** | **JSON Viewer Pro** | Dev Suite | Phân tích và định dạng chuỗi dữ liệu GPS thô từ server. |
| **2** | **Postman** | Dev Suite | Test nhanh các Endpoint API nhận/gửi dữ liệu điều phối xe. |
| **3** | **ColorZilla** | Productivity | Kiểm tra mã màu chuẩn (HEX/RGB) trên giao diện bản đồ và biểu đồ. |
| **4** | **VisBug** | Productivity | Tinh chỉnh nhanh UI/UX giao diện Dashboard trực tiếp khi họp review. |
| **5** | **uBlock Origin** | Security | Chặn quảng cáo, script theo dõi độc hại, giúp giảm tải băng thông. |

### 2.2. Thao tác ghim lên Toolbar
1. Click vào biểu tượng **Mảnh ghép (Tiện ích)** ở góc trên bên phải trình duyệt.
2. Tìm từng Extension trong danh sách trên.
3. Click vào biểu tượng **Ghim (Pin)** để đưa công cụ ra thanh công cụ (Toolbar) phục vụ thao tác 1-click.

### 2.3. Cấu hình phân quyền an toàn (Site Access: On Click)
Để tránh rủi ro các Extension đọc trộm dữ liệu GPS nhạy cảm trên hệ thống nội bộ:

1. Click chuột phải vào biểu tượng Extension trên Toolbar.
2. Di chuột đến mục **"Có thể đọc và thay đổi dữ liệu trang web"** (*This can read and change site data*).
3. Chọn **"Khi bạn nhấp vào tiện ích"** (*When you click the extension*).

> **Lưu ý:** Tất cả Extension (trừ uBlock Origin và JSON Viewer) bắt buộc phải cấu hình về chế độ *On Click*.

---

## 3. KIỂM THỬ & GIÁM SÁT HIỆU NĂNG (RAM / CPU)

Nếu trình duyệt xuất hiện hiện tượng giật/lag hoặc làm chậm tiến trình tải biểu đồ Waterfall trong DevTools, kỹ sư thực hiện quy trình kiểm tra theo các bước sau:

### Bước 1: Mở Trình quản lý tác vụ của Trình duyệt (Task Manager)
* Nhấn tổ hợp phím **`Shift` + `Esc`** trên trình duyệt (hoặc vào **Menu 3 chấm** > **Công cụ khác** > **Trình quản lý tác vụ**).
* *Lưu ý:* **Không** dùng trang `chrome://extensions` để đo tài nguyên, vì trang này chỉ hỗ trợ Bật/Tắt/Xóa tiện ích.

### Bước 2: Phân tích chỉ số tài nguyên
* Lọc các tiến trình có tiền tố **`Extension:`** (Tiện ích mở rộng).
* Kiểm tra 2 cột chính:
  * **Memory footprint (Bộ nhớ):** Nếu Extension chiếm **> 150 MB**, tiến hành đánh giá lại.
  * **CPU:** Nếu Extension giữ chỉ số CPU **> 10-20%** liên tục khi trang web đang nhàn rỗi, đó là nguyên nhân gây lag Waterfall.

### Bước 3: Xử lý sự cố
* Select Extension gây hao tốn tài nguyên -> Chọn **Kết thúc tiến trình (End Process)** để giải phóng RAM/CPU ngay lập tức.
* Truy cập `chrome://extensions` để **Tắt (Disable)** Extension đó và báo cáo lại cho Trưởng nhóm Kỹ thuật.
