# Hướng Dẫn Cấu Hình Tiện Ích Mở Rộng (Browser Extensions)
**Dành cho Đội ngũ Kỹ sư GreenLogistics**

---

## Phần 1. Phân Tích

### Vai trò của JSON Viewer
Khi gỡ lỗi (debug) API dữ liệu GPS từ máy chủ, dữ liệu thô trả về thường ở dạng chuỗi văn bản liền dòng (minified/raw text) rất khó đọc. **JSON Viewer** giúp:
* **Tự động định dạng (Format & Beautify):** Tách hàng, lùi đầu dòng và tô màu cú pháp (Syntax Highlighting) giúp kỹ sư phát hiện nhanh các thuộc tính toạ độ, trạng thái xe.
* **Cây dữ liệu tương tác (Interactive Tree View):** Cho phép thu gọn/mở rộng các node dữ liệu phức tạp để tập trung vào phần thông tin cần kiểm tra.
* **Tăng tốc gỡ lỗi:** Giảm thiểu thời gian đọc thủ công, tránh sai sót khi soi payload GPS lớn.

---

## Phần 2. Triển Khai

### 1. Danh sách 5 Tiện ích Thiết yếu

| Nhóm | Tiện ích (Extension) | Chức năng chính |
| :--- | :--- | :--- |
| **Dev Suite** | **JSON Formatter / JSON Viewer** | Soi, định dạng và làm đẹp dữ liệu JSON từ API GPS. |
| **Dev Suite** | **Postman / RESTClient** | Kiểm thử và gửi request API nhanh ngay trên trình duyệt. |
| **Productivity** | **ColorZilla** | Trích xuất và chấm mã màu (Color Picker) giao diện điều phối. |
| **Productivity** | **VisBug** | Chỉnh sửa và kiểm tra nhanh phần tử UI/UX trực quan. |
| **Security** | **uBlock Origin** | Chặn quảng cáo độc hại và script ngầm làm chậm hệ thống. |

### 2. Hướng dẫn Ghim Tiện ích lên Toolbar
1. Nhấp vào biểu tượng **Mảnh ghép puzzle** (Extensions) ở góc trên bên phải trình duyệt.
2. Tìm từng tiện ích trong danh sách 5 tiện ích trên.
3. Nhấp vào biểu tượng **Ghim (Pin)** bên cạnh tiện ích để đưa ra thanh công cụ (Toolbar) giúp thao tác nhanh.

### 3. Phân quyền Site Access (Hạn chế quyền đọc dữ liệu)
Để bảo vệ dữ liệu nhạy cảm của GreenLogistics:
1. Nhấp chuột phải vào biểu tượng Tiện ích trên Toolbar.
2. Chọn **This Can Read and Change Site Data** (Có thể đọc và thay đổi dữ liệu trang web).
3. Chọn chế độ **When You Click the Extension** (On click).

---

## Phần 3. Kiểm Thử & Tối Ưu Tài Nguyên

Nếu trình duyệt bị lag hoặc biểu đồ Waterfall nạp chậm, thực hiện kiểm tra tài nguyên theo quy trình sau:

1. **Mở Trình quản lý tác vụ trình duyệt (Browser Task Manager):**
   * Nhấn tổ hợp phím `Shift + Esc` (trên Chrome/Edge).
   * *Hoặc* Nhấp vào **Menu 3 chấm** $\rightarrow$ **More Tools** $\rightarrow$ **Task Manager**.
2. **Kiểm tra mức tiêu thụ RAM/CPU:**
   * Tìm các dòng có tiền tố `Extension: [Tên tiện ích]`.
   * Sắp xếp theo cột **Memory footprint** (Dung lượng RAM) để tìm tiện ích chiếm dụng tài nguyên lớn nhất.
3. **Xử lý tiện ích gây ngốn tài nguyên:**
   * Truy cập `chrome://extensions`.
   * Tắt (Disable) hoặc Xoá (Remove) các tiện ích không cần thiết hoặc gây giật lag hệ thống.