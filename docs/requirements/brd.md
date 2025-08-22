Dưới đây là danh sách các **feature chính** cần có cho hệ thống Logistic Portal, được tổng hợp từ tài liệu bạn cung cấp:

---

## **1. Người dùng và trải nghiệm**

* **Đối tượng người dùng**

    * Bên ngoài: Forwarder, Carrier, Khách hàng cá nhân.
    * Nội bộ: Nhân viên Sales, Vận hành, Quản lý.
* **Trải nghiệm người dùng**

    * Cổng dịch vụ online qua Web và Mobile.
    * Thông báo qua Email/SMS/Mobile.

---

## **2. Bảo mật và truy cập**

* **Hệ thống**

    * Áp dụng nguyên tắc Zero Trust khi thiết kế tích hợp (API/EDI).
    * Tuân thủ OWASP API Security Top 10 (2023).
* **Ứng dụng**

    * Security by Design trong quá trình phát triển.
    * Kiểm tra, loại bỏ lỗ hổng từ giai đoạn sớm.

---

## **3. Tích hợp**

* **Công nghệ**

    * API (Open API), EDI.
* **Hệ thống tích hợp**

    * Nội bộ: CMS, SAP, BI, sensor data, queue management.
    * Bên ngoài: IATA OneRecord, hệ thống Forwarder, Airlines, ALSx, Hải quan, Security, Ground Handling.
* **Kiến trúc**

    * Định hướng SOA/Microservices để mở rộng linh hoạt.

---

## **4. Tính năng cốt lõi của ứng dụng**

### **Dịch vụ & Giao dịch**

* Đăng ký trực tuyến.
* Yêu cầu dịch vụ online:

    * Nhập tay.
    * Upload file điện tử (AWB).
    * OCR từ ảnh/quét.
* Quản lý thông tin khách hàng và yêu cầu dịch vụ.
* Tạo số thứ tự & xếp hàng online.
* Thanh toán trực tuyến.
* Tra cứu trạng thái (Track & Trace).
* Hiển thị lịch bay.
* Thông báo Email/Mobile.

### **Hỗ trợ khách hàng**

* Self-service: Hướng dẫn trực quan, Chatbot/FAQ, Audit Trail.
* Raise Ticket, giao tiếp và SLA.
* Click-to-call với bộ phận hỗ trợ.

---

## **5. Phân tích và báo cáo**

* Dashboard phân tích:

    * KPI dịch vụ khách hàng.
    * KPI bán hàng.
    * KPI hài lòng khách hàng.
    * Hiệu suất vận hành.
    * Phân tích hành trình khách hàng (Customer Journey).

---

## **6. Quản trị hệ thống**

* Authentication, quản lý và phân quyền nhân viên.
* Quản lý dữ liệu tuân thủ chuẩn (IATA OneRecord, Nghị định 13/2023 về bảo vệ dữ liệu cá nhân).
* DBMS: Oracle được ưu tiên.

---

## **7. Hạ tầng & vận hành**

* Đảm bảo liên tục kinh doanh: backup, DR.
* Giám sát và ứng phó sự cố tự động.
* Đào tạo, nâng cao năng lực đội ngũ (PM, BA, DA).

---

## **8. Lộ trình triển khai (Các Sprint MVP)**

* **Sprint 0**: Chuẩn hóa BRD.
* **Sprint 1**: Nền tảng kỹ thuật, Auth, Role, Dashboard.
* **Sprint 2A (MVP1)**: Đăng ký, yêu cầu dịch vụ.
* **Sprint 2B (MVP2)**: FAQ, Raise Ticket, Knowledge base.
* **Sprint 3 (MVP3)**: Queue, Track & Trace, KPI Dashboard.
* **Sprint 4 (MVP4)**: OCR, Thanh toán, Lịch bay, Chatbot, Customer Journey Analytics.

