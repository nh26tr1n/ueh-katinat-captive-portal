# Katinat Captive Portal (NHOM 8) 

## 1. Tổng quan

**Hệ thống Captive Portal cho chuỗi cà phê Katinat Saigon Cafe**

Ứng dụng cung cấp trang đăng nhập Wi-Fi cho khách hàng, nơi họ có thể xác thực thông qua Google OAuth, Facebook OAuth hoặc Email OTP. Hệ thống lưu trữ hồ sơ khách hàng và các phiên Wi-Fi trên Supabase, đồng thời cung cấp cho quản trị viên các công cụ để xem dữ liệu, quản lý voucher và gửi email marketing.

> **Lưu ý:** Kho lưu trữ (repository) này chứa ứng dụng Next.js. Cấu hình thiết bị OpenWrt/NoDogSplash và cấu hình gateway mạng được quản lý riêng biệt.

## 2. Công nghệ sử dụng

| Công nghệ | Vai trò trong dự án |
|---|---|
| Next.js 14 (App Router) | Xây dựng giao diện, Server Components và API Routes |
| React 18 & TypeScript | Phát triển UI tương tác và đảm bảo an toàn kiểu dữ liệu |
| Tailwind CSS & shadcn/ui | Thiết kế giao diện và các component tái sử dụng |
| Supabase | Cơ sở dữ liệu PostgreSQL, hệ thống xác thực (Auth) và RLS |
| Zod | Kiểm tra và xác thực dữ liệu đầu vào |
| Brevo & Nodemailer | Gửi email marketing (Brevo) và email thủ công (Gmail SMTP) |
| Recharts & PapaParse | Vẽ biểu đồ thống kê và xử lý file CSV |

## 3. Chức năng chính

- Đăng nhập của khách qua Google OAuth, Facebook OAuth hoặc Email OTP, kèm theo form đồng ý và cập nhật hồ sơ.
- Ghi nhận và quản lý phiên Wi-Fi: bắt đầu, heartbeat và kết thúc, cũng như liên kết thiết bị.
- Bảng điều khiển Admin (Dashboard): xem khách hàng, phiên, thiết bị, sự đồng ý và số liệu thống kê.
- Quản lý và đổi voucher, quản lý chiến dịch, nhập CSV và gửi email marketing.

## 4. Cấu trúc mã nguồn

Dự án được tổ chức theo mô hình chuẩn của Next.js App Router, chia tách rõ ràng giữa giao diện (Frontend), xử lý nghiệp vụ (Backend/API) và các cấu hình hệ thống.

```text
katinat-guest-wifi/
├── app/                  # Chứa toàn bộ giao diện và API (Next.js App Router)
│   ├── (portal)/         # Các trang dành cho khách hàng (đăng nhập Wi-Fi, OTP)
│   ├── admin/            # Các trang quản trị (Dashboard, Voucher, Campaign...)
│   └── api/              # Các API xử lý nghiệp vụ (Auth, Session, Gửi mail...)
├── components/           # Các "mảnh ghép" giao diện dùng chung
│   ├── ui/               # Các thành phần cơ bản (Nút, Form, Bảng...)
│   ├── portal/           # Giao diện riêng cho trang khách hàng
│   └── admin/            # Giao diện riêng cho trang quản trị
├── lib/                  # Chứa logic cấu hình và kết nối hệ thống
│   ├── supabase/         # File kết nối Supabase (Client & Server)
│   ├── validations/      # Các schema kiểm tra dữ liệu bằng Zod
│   └── services/         # Các hàm xử lý dữ liệu (Customer, Session, Voucher...)
├── types/                # Chứa các định nghĩa kiểu dữ liệu (TypeScript)
├── supabase/migrations/  # Các file SQL tạo bảng, hàm RPC và phân quyền (RLS)
├── locales/              # File ngôn ngữ (Tiếng Việt, Tiếng Anh)
├── public/               # Hình ảnh, logo, icon
└── middleware.ts         # File trung gian kiểm tra đăng nhập và quyền Admin

