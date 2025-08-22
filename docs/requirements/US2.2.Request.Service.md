# US 2.2: Request Service

## User Story
**US 2.2:** Là khách hàng, tôi muốn gửi yêu cầu dịch vụ bằng nhiều cách.

## Tasks

### Task 1: Nhập tay thông tin lô hàng
- **Mô tả:** Cho phép khách hàng nhập thủ công thông tin chi tiết về lô hàng
- **Acceptance Criteria:**
    - Giao diện form nhập liệu đầy đủ các trường thông tin
    - Validation dữ liệu đầu vào
    - Lưu trữ thông tin vào hệ thống

### Task 2: Upload file AWB (Excel/PDF)
- **Mô tả:** Hỗ trợ tải lên và xử lý file AWB định dạng Excel hoặc PDF
- **Acceptance Criteria:**
    - Chấp nhận file Excel (.xlsx, .xls) và PDF
    - Parse và extract thông tin từ file
    - Validate định dạng và nội dung file
    - Hiển thị thông tin đã extract để xác nhận

### Task 3: OCR đọc thông tin từ ảnh/quét
- **Mô tả:** Sử dụng OCR để đọc thông tin từ ảnh chụp hoặc file scan
- **Acceptance Criteria:**
    - Hỗ trợ các định dạng ảnh phổ biến (JPG, PNG, PDF)
    - OCR engine đọc và extract text
    - Áp dụng AI/ML để nhận diện các trường thông tin
    - Cho phép chỉnh sửa thông tin sau khi OCR

## Technical Requirements
- API endpoints cho từng phương thức nhập liệu
- File upload handling với size limit
- OCR integration (Google Vision API, Tesseract, etc.)
- Data validation và sanitization
- Error handling và user feedback

## Dependencies
- OCR service/library
- File processing utilities
- Frontend form components