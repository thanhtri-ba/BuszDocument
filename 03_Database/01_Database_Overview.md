# Database Overview

Project: BusZ - Intercity Bus Ticket Booking Platform

Module: Database

Document ID: DB-001

Priority: Critical

Status: Draft

Version: 1.0

---

# 1. Purpose

Tài liệu này mô tả tổng quan cơ sở dữ liệu của hệ thống BusZ.

Database là nền tảng lưu trữ toàn bộ dữ liệu của hệ thống:

- User
- Booking
- Trip
- Seat
- Payment
- Ticket
- Notification
- Review
- Report
- Admin

---

# 2. Database Technology

Primary Database:

PostgreSQL

ORM:

Prisma

Cache:

Redis

Storage:

Supabase Storage

---

# 3. Database Design Goals

Database phải đảm bảo:

- Dữ liệu chính xác
- Không trùng booking
- Không trùng ghế
- Không mất giao dịch
- Dễ mở rộng
- Dễ backup
- Dễ migrate

---

# 4. Main Database Domains

Database được chia thành các nhóm chính:

```text
Authentication
User Profile
Bus Company
Route
Trip
Seat
Booking
Payment
Ticket
Promotion
Notification
Review
Report
Audit Log
System Settings
```

---

# 5. Core Tables

Các bảng cốt lõi:

```text
users
roles
permissions
user_roles

bus_companies
buses
drivers
routes
locations
stations

trips
trip_seats
seat_layouts

bookings
booking_passengers
booking_contacts
booking_locations

payments
payment_transactions
refunds

tickets
ticket_qr_codes
ticket_checkins

promotions
vouchers

notifications
reviews
audit_logs
```

---

# 6. High Level Relationship

```text
User
 ↓
Booking
 ↓
Payment
 ↓
Ticket
 ↓
Check-in
```

```text
Bus Company
 ↓
Bus
 ↓
Trip
 ↓
Seat
 ↓
Booking
```

---

# 7. Database Principles

## 7.1 Use UUID

Tất cả bảng chính nên dùng UUID làm Primary Key.

Ví dụ:

```sql
id UUID PRIMARY KEY
```

---

## 7.2 Soft Delete

Không xóa cứng dữ liệu quan trọng.

Các bảng nên có:

```text
deleted_at
```

---

## 7.3 Timestamp

Mỗi bảng nên có:

```text
created_at
updated_at
deleted_at
```

---

## 7.4 Audit Log

Các hành động quan trọng phải ghi log:

- Login
- Booking
- Payment
- Refund
- Ticket
- Admin Action

---

# 8. Naming Convention

Table name:

```text
snake_case
plural
```

Ví dụ:

```text
users
bookings
payment_transactions
ticket_qr_codes
```

Column name:

```text
snake_case
```

Ví dụ:

```text
full_name
created_at
booking_status
```

---

# 9. Status Fields

Status nên dùng enum hoặc string có kiểm soát.

Ví dụ Booking Status:

```text
PENDING
CONFIRMED
PAID
CANCELLED
REFUNDED
COMPLETED
```

Payment Status:

```text
CREATED
PENDING
PROCESSING
SUCCESS
FAILED
REFUNDED
```

Ticket Status:

```text
ACTIVE
CHECKED_IN
CANCELLED
REFUNDED
EXPIRED
```

---

# 10. Index Strategy

Cần index cho:

```text
email
phone
booking_code
ticket_number
payment_transaction_id
trip_id
user_id
created_at
status
```

---

# 11. Transaction Strategy

Các nghiệp vụ bắt buộc dùng database transaction:

- Create Booking
- Hold Seat
- Payment Success
- Generate Ticket
- Refund
- Cancel Ticket
- Check-in

---

# 12. Data Integrity Rules

Không được:

- Một ghế bán cho hai người
- Một payment tạo nhiều ticket trùng
- Một booking thanh toán nhiều lần
- Một QR check-in nhiều lần
- Refund vượt quá số tiền đã thanh toán

---

# 13. Backup Strategy

Backup hằng ngày.

Backup trước khi migration.

Giữ bản backup tối thiểu 30 ngày.

Production backup nên lưu ở storage riêng.

---

# 14. Migration Strategy

Dùng Prisma Migration.

Không sửa database thủ công trên production.

Mỗi thay đổi schema phải có migration file.

---

# 15. Related Documents

- 01_Business
- 02_UI
- 04_Backend
- 05_API
- 13_Security
- 14_DevOps

---

# 16. Summary

Database Overview là tài liệu nền tảng cho toàn bộ thiết kế cơ sở dữ liệu BusZ.

Các tài liệu tiếp theo sẽ đi chi tiết vào ERD, bảng dữ liệu, quan hệ, index, constraint và Prisma Schema.