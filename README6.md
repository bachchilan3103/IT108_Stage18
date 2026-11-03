# Hướng Dẫn Phân Tích & Tối Ưu Hiệu Năng Trang Chủ Portal GreenLogistics

**Hệ thống:** `portal.greenlog.vn`  
**Mục tiêu:** Xử lý sự cố nạp chậm (20s) và mất hình ảnh bản đồ lộ trình cho Shipper.

---

## Phần 1. Phân Tích Nguyên Nhân (Performance & Network Analysis)

Khi gỡ lỗi trên Google Chrome DevTools (Thẻ **Network**, bật tùy chọn **Disable Cache**):

### 1. Nút thắt cổ chai 1: Đứt gãy tuyến nạp tài nguyên (Lỗi 404 Not Found)
* **Dấu hiệu:** Các đường dẫn gọi đến file CSS, JavaScript hoặc SDK bản đồ hiển thị màu đỏ với mã trạng thái `404 Not Found`.
* **Tác động:** Trình duyệt không thể nạp các file định dạng giao diện và kịch bản điều khiển. Việc thiếu mã JS/CSS quan trọng làm chặn quá trình tô vẽ giao diện (Render-blocking), khiến hình ảnh bản đồ lộ trình không hiển thị và trang bị treo.

### 2. Nút thắt cổ chai 2: Dung lượng tệp quá lớn kéo dài biểu đồ Waterfall
* **Dấu hiệu:** Các tệp hình ảnh có kích thước vài Megabytes xuất hiện trên biểu đồ Waterfall với thanh kéo dài ở các giai đoạn *Waiting (TTFB)* và *Content Download*.
* **Tác động:** Các tệp ảnh dung lượng cao chiếm dụng toàn bộ băng thông mạng, đẩy các request dữ liệu GPS và bản đồ vào hàng chờ (Queueing), kéo dài tổng thời gian tải trang lên đến 20 giây.

---

## Phần 2. Triển Khai Quy Trình 3 Bước Tối Ưu

### Bước 1: Sửa lỗi đứt link (Status 404)
1. Trong thẻ **Network**, lọc các request bị tô đỏ (`Status: 404`).
2. Kiểm tra lại đường dẫn tệp trên Web Server hoặc CDN.
3. Đảm bảo toàn bộ các file CSS và JavaScript cốt lõi trả về mã **`200 OK`** trước khi trình duyệt thực hiện Rendering Engine.

### Bước 2: Nén và tối ưu hóa tài nguyên hình ảnh
1. Chuyển đổi toàn bộ định dạng ảnh sang `.webp` hoặc `.avif` để giảm dung lượng nhưng vẫn giữ nguyên chất lượng.
2. Áp dụng kỹ thuật Lazy Loading (`loading="lazy"`) cho các hình ảnh không nằm trong khung hình đầu tiên.
3. Nén dung lượng tệp tĩnh xuống mức tối ưu (< 200KB/ảnh).

### Bước 3: Bật cơ chế Browser Cache cho tài nguyên tĩnh
1. Cấu hình HTTP Header trên máy chủ (Nginx/Apache) cho các tài nguyên tĩnh (`.css`, `.js`, `.png`, `.jpg`, `.woff2`).
2. Thiết lập header: `Cache-Control: public, max-age=31536000, immutable`.

---

## Phần 3. Kiểm Thử & Giải Thích Mã 304 Not Modified

Khi tải lại trang (không bật *Disable Cache*), trình duyệt trả về mã **`304 Not Modified`**.

### Giải thích ý nghĩa của mã 304:
* **Cơ chế hoạt động:** Trình duyệt gửi request kèm mã định danh `ETag` hoặc `If-Modified-Since`. Máy chủ xác nhận tài nguyên trên server không có thay đổi so với bản lưu trữ tạm thời (Cache) của trình duyệt.
* **Lợi ích đối với hệ thống GreenLogistics:**
  * **Tiết kiệm băng thông:** Máy chủ chỉ trả về thông báo Header nhẹ (vài trăm bytes) thay vì phải tải lại toàn bộ nội dung tệp (vài Megabytes).
  * **Tăng tốc độ hiển thị:** Trình duyệt sử dụng trực tiếp bản sao từ RAM/Disk Cache, giúp trang `portal.greenlog.vn` nạp tức thì (< 1 giây).
  * **Giảm tải máy chủ:** Server trung tâm không bị quá tải I/O, tập trung tài nguyên xử lý các request API GPS thời gian thực cho Shipper.