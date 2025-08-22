Dưới đây là bản **Product Requirements** cho **US 2.1 – Đăng ký tài khoản online** (bao gồm form đăng ký, xác thực email/SMS và lưu trữ Oracle).

---

# 1) Tổng quan & Phạm vi

* **User story**: *“Là khách hàng, tôi muốn đăng ký tài khoản online để sử dụng dịch vụ.”*
* **Nằm trong**: Sprint 2A – MVP Online Service Delivery (có “Online registration”).&#x20;
* **Nền tảng/Định hướng**: Portal web/mobile; bảo mật theo Zero Trust, OWASP API Top 10; DB ưu tiên Oracle; tuân thủ PDPD 13/2023. &#x20;

## Mục tiêu thành công (Success Metrics)

* ≥ 95% đăng ký hoàn tất (OTP xác thực thành công) trong ≤ 10 phút.
* Tỷ lệ gửi OTP thành công ≥ 99%, độ trễ < 10s/OTP.
* Không có tài khoản trùng email/điện thoại (duy nhất/uniqueness đảm bảo).&#x20;

---

# 2) Tiền điều kiện & Giả định

* Có kênh gửi email (SMTP) và SMS (provider) sẵn.
* Cấu hình **CAPTCHA** để chống bot.
* Chính sách bảo mật & đồng ý xử lý dữ liệu (PDPD 13/2023) hiển thị khi đăng ký.&#x20;

---

# 3) Yêu cầu chức năng (Functional Requirements)

## 3.1 Form đăng ký

**Trường bắt buộc**

* Email (định dạng hợp lệ, duy nhất), Số điện thoại (E.164, duy nhất), Mật khẩu, Họ tên, Tên công ty (nếu có), Quốc gia/Khu vực, Đồng ý điều khoản & chính sách PDPD.
  **Trường tuỳ chọn**
* Mã số thuế/Business ID, Vai trò (Forwarder/Carrier/Individual).
  **Ràng buộc/Rule**
* Mật khẩu: tối thiểu 8 ký tự, có chữ hoa, chữ thường, số (và ký tự đặc biệt khuyến nghị).
* Email/SĐT **unique** trong hệ thống (validation ở BE + chỉ báo UI).
* Bật CAPTCHA trước khi submit.
* UI hiển thị link Chính sách bảo vệ dữ liệu cá nhân (PDPD) & Điều khoản. &#x20;

## 3.2 Luồng xác thực Email/SMS (OTP)

* Sau khi submit, hệ thống tạo hồ sơ **Pending Verification** và gửi **OTP** đến **email** *và/hoặc* **SMS** (cấu hình được).
* **OTP**: 6 chữ số, hiệu lực 10 phút, tối đa 5 lần nhập sai khoá tài khoản tạm thời (15 phút).
* **Resend**: tối đa 3 lần/giờ/kênh, chờ 60s giữa các lần resend.
* **Khóa chống lạm dụng**: rate-limit per IP/email/phone; phát hiện đăng ký hàng loạt.
* Xác thực thành công → trạng thái **Active**; gửi email “Welcome”.
* Hỗ trợ **đổi kênh OTP** (nếu email không nhận được, dùng SMS và ngược lại).

## 3.3 Quản lý trùng lặp & khôi phục

* Nếu email/phone đã tồn tại **Active** → chặn đăng ký, gợi ý **Quên mật khẩu**.
* Nếu tồn tại bản ghi **Pending** chưa hết hạn → cho **resend OTP**.

## 3.4 Ghi vết/Audit & Thông báo

* Ghi vết các sự kiện: submit, gửi OTP, xác thực thành công/thất bại, resend, khóa tạm.
* Gửi email/sms thông báo theo trạng thái (đăng ký, xác thực, khoá tạm).
* Tuân theo **OWASP API Security Top 10** (kiểm soát rate limit, authZ, log).&#x20;

---

# 4) API Contract (đề xuất)

**POST /auth/register**

* Req: {email, phone, password, full\_name, company, country, consent=true, captcha\_token}
* Res (201): {user\_id, verification\_channel: “email|sms|both”}
* Error: 400 (validation), 409 (duplicated), 429 (rate limit)

**POST /auth/verify**

* Req: {user\_id, channel: “email|sms”, otp}
* Res (200): {status:“verified”}
* Error: 400/401 (OTP sai/hết hạn), 423 (locked), 429 (rate limit)

**POST /auth/resend-otp**

* Req: {user\_id, channel}
* Res (200): {status:“resent”}
* Error: 404 (không còn pending), 429 (vượt giới hạn)

---

# 5) Mô hình dữ liệu (Postgres – đề xuất)

**TABLE USERS**

* user\_id (PK, UUID), email (UNIQUE), phone (UNIQUE), password\_hash, full\_name, company, country, status (PENDING/ACTIVE/LOCKED), created\_at, updated\_at, consent\_ts, consent\_version

**TABLE USER\_VERIFICATIONS**

* id (PK), user\_id (FK), channel (EMAIL/SMS), otp\_hash, expire\_at, attempt\_count, resend\_count, last\_sent\_at, status (PENDING/VERIFIED/EXPIRED/LOCKED)

**TABLE AUDIT\_LOGS**

* id (PK), user\_id (nullable), event (REGISTER\_SUBMIT/OTP\_SENT/OTP\_VERIFY\_SUCCESS/…),
  ip, user\_agent, meta (JSON), created\_at

> Ghi chú: DBMS ưu tiên Oracle.&#x20;

---

# 6) Bảo mật & Tuân thủ

* **Zero Trust + OWASP API Top 10** cho các API đăng ký/xác thực: input validation, authn flow, rate-limiting, sensitive data exposure, logging an toàn.&#x20;
* **PDPD 13/2023**: minh bạch mục đích thu thập, lưu dấu **consent\_ts** & **consent\_version**, cơ chế rút lại consent/xoá dữ liệu khi yêu cầu.&#x20;
* **Mật khẩu & OTP**: chỉ lưu dạng hash (không lưu plaintext), TTL ngắn, không lộ chi tiết trong log.
* **Anti-automation**: CAPTCHA + IP/device fingerprint (tuỳ chọn).

---

# 7) Phi chức năng (NFR)

* **Hiệu năng**: tạo user + gửi OTP ≤ 2s (không tính độ trễ nhà mạng).
* **Khả dụng**: 99.9% cho luồng đăng ký.
* **Khả mở rộng**: chịu ≥ 50 req/s đăng ký đồng thời (burst).
* **Khả quan sát**: metrics gửi OTP, tỉ lệ fail, retry; dashboard theo dõi (liên quan KPI portal).&#x20;
* **Khôi phục sự cố/DR**: dữ liệu đăng ký được backup theo chính sách BC/DR.&#x20;

---

# 8) Acceptance Criteria (Gherkin)

```gherkin
Feature: Online Registration

Scenario: Submit form hợp lệ → tạo tài khoản ở trạng thái PENDING & gửi OTP
  Given tôi ở trang đăng ký
  And tôi nhập email hợp lệ, số điện thoại hợp lệ, mật khẩu hợp lệ và đồng ý điều khoản
  When tôi bấm "Đăng ký"
  Then hệ thống tạo user với status "PENDING"
  And hệ thống gửi OTP qua kênh đã chọn
  And tôi thấy thông báo yêu cầu xác thực OTP

Scenario: Email đã tồn tại
  Given một tài khoản ACTIVE dùng email A tồn tại
  When tôi đăng ký với email A
  Then hệ thống trả 409 và gợi ý "Quên mật khẩu"

Scenario: Xác thực OTP thành công
  Given tôi có user ở trạng thái PENDING và OTP còn hiệu lực
  When tôi nhập đúng OTP
  Then hệ thống cập nhật status "ACTIVE"
  And gửi email chào mừng

Scenario: OTP sai quá 5 lần
  Given tôi có user ở trạng thái PENDING
  When tôi nhập sai OTP 5 lần
  Then hệ thống khóa tạm verification 15 phút
  And hiển thị thông báo thử lại sau

Scenario: Resend OTP vượt giới hạn
  Given tôi đã resend OTP 3 lần trong 1 giờ
  When tôi yêu cầu resend lần nữa
  Then hệ thống trả 429 "Too Many Requests"
```

---

# 9) Kiểm thử & Quan sát (High-level)

* **Unit**: validation email/phone/password; sinh & hash OTP; rate limit.
* **Integration**: SMTP/SMS provider (timeout, retry, fallback kênh).
* **E2E**: Happy path, duplicate, OTP expire, locked, resend limit, captcha fail.
* **Monitoring**: tỷ lệ gửi OTP, tỷ lệ verify, error code 4xx/5xx, thời gian phản hồi.

---

# 10) Phạm vi ngoài (Out of scope – US này)

* Đăng nhập, quên mật khẩu (story khác).
* KYC nâng cao/eKYC (story khác).
* Federation/SSO doanh nghiệp (nếu cần, tách epic).

---

# 11) Phụ thuộc

* Cấu hình kênh Email/SMS, CAPTCHA.
* Module quản trị người dùng & phân quyền (từ Sprint 1).&#x20;

---

Bạn muốn mình **xuất bản PRD này thành file .docx hoặc .xlsx** (kèm bảng trường dữ liệu, API, checklist test) để import vào Jira/Confluence không?
