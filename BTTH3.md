# Phần 1. Báo cáo phân tích (IPO)

## 1. Input (Đầu vào - Dấu hiệu và Tài nguyên)
*   **Tình trạng:** Mất điện thoại chứa ứng dụng Google Authenticator sinh mã TOTP, dẫn đến việc bị khóa ngoài (Lockout) không thể đăng nhập vào AWS.
*   **Tài nguyên cần có:** **Bộ mã khôi phục dự phòng (Backup / Recovery Codes)** đã được Lead Developer in ra giấy cất ở két sắt vật lý hoặc lưu an toàn trong mục Secure Note của Bitwarden từ trước.

## 2. Process (Quy trình xử lý khẩn cấp - 3 Bước)
*   **Bước 1: Xác thực vượt rào (Bypass 2FA):** 
    Trên một máy tính an toàn, tiến hành đăng nhập AWS bằng Username và Password. Tại bước hệ thống đòi mã xác thực 6 số, bấm chọn phương thức thay thế *"Sử dụng mã khôi phục (Use a recovery code)"*. Lấy 1 mã trong bộ Backup Codes nhập vào để vượt qua lớp bảo vệ. *(Lưu ý: Mã này chỉ có hiệu lực sử dụng đúng 1 lần, nhập xong hệ thống sẽ tự động gạch bỏ).*
*   **Bước 2: Hủy thiết bị cũ (Revoke):**
    Ngay khi vào được bảng điều khiển AWS (IAM / Security Credentials), lập tức tìm đến mục MFA (Multi-Factor Authentication) và **Xóa (Remove/Revoke)** liên kết với ứng dụng Authenticator của chiếc điện thoại đã mất. Thao tác này biến ứng dụng Google Authenticator trên chiếc điện thoại kia thành "đồ bỏ đi", dù kẻ gian có mở được máy cũng không thể dùng mã sinh ra trên đó để truy cập AWS nữa.
*   **Bước 3: Cấp thiết bị và bộ mã mới (Re-enroll):**
    Tiến hành kích hoạt lại tính năng 2FA từ đầu bằng cách lấy một chiếc điện thoại/thiết bị sạch mới quét mã QR (Shared Secret Key) mới do AWS cấp. Cuối cùng, tạo và lưu trữ lại một bộ Backup Codes mới để thay thế cho bộ cũ đã sử dụng.

## 3. Output (Đầu ra - Hệ thống an toàn)
*   Lấy lại hoàn toàn quyền quản trị hệ thống AWS để tiếp tục bảo trì.
*   Thiết bị điện thoại bị mất hoàn toàn mất khả năng xác thực (bị vô hiệu hóa triệt để).
*   Lớp bảo mật 2FA được thiết lập lại vững chắc trên thiết bị mới.

---

# Phần 2. Phân tích rủi ro: Cấm lưu Backup Codes dưới dạng ảnh chụp

Tuyệt đối không được chụp ảnh màn hình (Screenshot) chứa các mã Backup Codes và lưu trong thư viện ảnh (Gallery) của điện thoại vì 2 lý do chí mạng sau:

1.  **Giao trứng cho ác (Rủi ro mất cắp vật lý):** Backup Code có quyền lực mạnh ngang ngửa, thậm chí mạnh hơn mã TOTP vì nó vượt qua mọi rào cản thời gian. Nếu kẻ gian nhặt được điện thoại, bẻ khóa được màn hình và mở thư viện ảnh ra, chúng sẽ có ngay chìa khóa vạn năng. Nếu trên máy đó bạn còn để chế độ lưu mật khẩu tự động ở trình duyệt, kẻ gian sẽ có cả Mật khẩu lẫn Backup Code, chiếm đoạt AWS dễ dàng.
2.  **Mồi ngon cho mã độc (Rủi ro phần mềm):** Nếu điện thoại vô tình cài các ứng dụng rác yêu cầu quyền "Truy cập tệp và ảnh", hoặc máy bị nhiễm mã độc trích xuất dữ liệu (Infostealer), các đoạn mã độc này được lập trình để tự động quét thư viện ảnh, chạy công nghệ nhận diện chữ (OCR) và gửi các chuỗi mã dự phòng này thẳng về máy chủ của tin tặc.