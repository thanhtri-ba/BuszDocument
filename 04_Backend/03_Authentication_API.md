# Authentication API

Project

BusZ - Intercity Bus Ticket Booking Platform

Module

Backend API

Document ID

API-003

Priority

Critical

Version

1.0

---

# 1. Purpose

Authentication API chịu trách nhiệm:

- Đăng ký
- Đăng nhập
- Đăng xuất
- Refresh Token
- Quên mật khẩu
- Đặt lại mật khẩu
- Xác minh Email
- Đổi mật khẩu
- Quản lý JWT
- Quản lý Session

Đây là Gateway bảo mật của toàn bộ hệ thống.

---

# 2. Authentication Flow

```
Register

↓

Verify Email

↓

Login

↓

JWT

↓

Protected APIs

↓

Refresh Token

↓

Logout
```

---

# 3. Authentication Architecture

```
Flutter

↓

REST API

↓

Auth Controller

↓

Auth Service

↓

JWT

↓

Prisma

↓

PostgreSQL
```

---

# 4. Endpoints Overview

| Method | Endpoint | Auth |
|---------|----------|------|
| POST | /auth/register | ❌ |
| POST | /auth/login | ❌ |
| POST | /auth/logout | ✅ |
| POST | /auth/refresh | ❌ |
| POST | /auth/forgot-password | ❌ |
| POST | /auth/reset-password | ❌ |
| POST | /auth/change-password | ✅ |
| POST | /auth/verify-email | ❌ |
| POST | /auth/resend-verification | ❌ |
| GET | /auth/me | ✅ |

---

# 5. Register

Endpoint

```
POST /auth/register
```

Purpose

Tạo tài khoản mới.

Request

```json
{
  "email": "user@gmail.com",
  "password": "Password123!",
  "fullName": "Nguyen Van A",
  "phone": "0912345678"
}
```

Response

```json
{
  "success": true,
  "message": "Register successfully. Please verify your email."
}
```

Business Rules

- Email duy nhất.
- Password được BCrypt.
- Tạo Profile mặc định.
- Gửi Email Verification.

---

# 6. Login

Endpoint

```
POST /auth/login
```

Request

```json
{
  "email": "user@gmail.com",
  "password": "Password123!"
}
```

Response

```json
{
  "accessToken": "...",
  "refreshToken": "...",
  "expiresIn": 3600,
  "user": {
    "id": "...",
    "email": "user@gmail.com",
    "role": "CUSTOMER"
  }
}
```

Business Rules

- Email đã xác minh.
- Account ACTIVE.
- BCrypt Verify.
- Ghi Audit Log.
- Tạo Session.

---

# 7. Refresh Token

Endpoint

```
POST /auth/refresh
```

Request

```json
{
  "refreshToken": "..."
}
```

Response

```json
{
  "accessToken": "...",
  "expiresIn": 3600
}
```

Rules

- Refresh Token còn hạn.
- Chưa bị thu hồi.
- Thuộc đúng User.

---

# 8. Logout

Endpoint

```
POST /auth/logout
```

Authorization

```
Bearer Token
```

Business Rules

- Thu hồi Refresh Token.
- Xóa Session.
- Ghi Audit.

---

# 9. Forgot Password

Endpoint

```
POST /auth/forgot-password
```

Request

```json
{
  "email":"user@gmail.com"
}
```

Business Flow

```
Check Email

↓

Generate Reset Token

↓

Send Email

↓

Expire 15 phút
```

---

# 10. Reset Password

Endpoint

```
POST /auth/reset-password
```

Request

```json
{
  "token":"...",
  "password":"NewPassword123!"
}
```

Rules

- Token hợp lệ.
- Token chưa hết hạn.
- Password mới khác Password cũ.

---

# 11. Change Password

Endpoint

```
POST /auth/change-password
```

Authorization

Required

Request

```json
{
  "oldPassword":"...",
  "newPassword":"..."
}
```

Rules

- Verify Password cũ.
- BCrypt Password mới.
- Thu hồi toàn bộ Session cũ.

---

# 12. Verify Email

Endpoint

```
POST /auth/verify-email
```

Request

```json
{
  "token":"..."
}
```

Rules

- Token đúng.
- Token chưa hết hạn.
- Chỉ Verify một lần.

---

# 13. Resend Verification

Endpoint

```
POST /auth/resend-verification
```

Rules

- Tối đa 5 lần/ngày.
- Cách nhau ít nhất 60 giây.

---

# 14. Current User

Endpoint

```
GET /auth/me
```

Authorization

Bearer Token

Response

```json
{
  "id":"...",
  "email":"...",
  "role":"CUSTOMER",
  "profile":{}
}
```

---

# 15. JWT Structure

Access Token

```
Header

Payload

Signature
```

Payload

```json
{
  "sub":"userId",
  "email":"user@gmail.com",
  "role":"CUSTOMER",
  "exp":123456
}
```

---

# 16. Session Management

Mỗi Login tạo:

```
Session

+

Refresh Token
```

Session lưu:

- Device
- IP
- Login Time
- Last Activity

---

# 17. Security

BCrypt

JWT

Refresh Rotation

HTTPS Only

HttpOnly Cookie (Web)

Secure Storage (Mobile)

Rate Limit

---

# 18. Error Codes

| Code | Description |
|------|-------------|
| AUTH_001 | Invalid Email |
| AUTH_002 | Invalid Password |
| AUTH_003 | Email Not Verified |
| AUTH_004 | Account Locked |
| AUTH_005 | Refresh Token Invalid |
| AUTH_006 | Session Expired |
| AUTH_007 | Password Too Weak |

---

# 19. Validation Rules

Email

RFC5322

Password

- ≥ 8 ký tự
- 1 chữ hoa
- 1 chữ thường
- 1 số
- 1 ký tự đặc biệt

Phone

E.164

---

# 20. Rate Limit

Login

```
10 requests / phút
```

Register

```
5 requests / giờ
```

Forgot Password

```
3 requests / giờ
```

---

# 21. Sequence Diagram

```
Flutter

↓

POST /auth/login

↓

Validate DTO

↓

Check User

↓

Verify Password

↓

Generate JWT

↓

Save Session

↓

Return Token
```

---

# 22. Test Cases

✓ Register.

✓ Duplicate Email.

✓ Login Success.

✓ Wrong Password.

✓ Refresh Token.

✓ Logout.

✓ Forgot Password.

✓ Reset Password.

✓ Verify Email.

---

# 23. Acceptance Criteria

✓ JWT hoạt động.

✓ Refresh Token Rotation.

✓ BCrypt Password.

✓ Email Verification.

✓ Audit Log.

✓ Session Management.

✓ Swagger hoàn chỉnh.

---

# 24. Related Documents

API-004 User API

API-017 API Security

DB-005 Users

DB-016 Audit Logs

---

# 25. Summary

Authentication API là nền tảng bảo mật của BusZ, cung cấp đầy đủ các chức năng xác thực, quản lý phiên đăng nhập, JWT, Refresh Token, xác minh email và quản lý mật khẩu. Toàn bộ API được thiết kế theo chuẩn RESTful, tích hợp Prisma ORM, PostgreSQL và NestJS để đảm bảo an toàn, hiệu năng và khả năng mở rộng.