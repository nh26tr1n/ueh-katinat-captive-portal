# Katinat Captive Portal (Nhóm 8)

## 1. Giới thiệu tổng quan

Katinat Captive Portal là hệ thống cung cấp trang đăng nhập Wi-Fi cho khách hàng tại Katinat Saigon Cafe. Khách xác thực để sử dụng mạng Wi-Fi; hệ thống lưu thông tin hồ sơ và phiên truy cập vào Supabase, đồng thời hỗ trợ quản trị viên theo dõi hoạt động và gửi email marketing đến những khách đã đồng ý nhận tin.

Dự án gồm ứng dụng web Next.js và các migration cơ sở dữ liệu Supabase. Tích hợp router/OpenWrt hoặc NoDogSplash được cấu hình riêng ở môi trường mạng; các cấu hình đó không nằm trong repository này.

## 2. Chức năng chính

### 2.1. Dành cho khách hàng (Guest Portal)

- Đăng nhập Wi-Fi bằng Google OAuth 2.0, Facebook OAuth hoặc Email OTP thông qua Supabase Auth.
- Xem và cập nhật hồ sơ cá nhân, gồm họ tên, giới tính và nhóm tuổi.
- Đồng ý hoặc từ chối nhận email marketing; lựa chọn được lưu để áp dụng cho hoạt động gửi tin.
- Tạo phiên Wi-Fi sau khi xác thực và duy trì trạng thái phiên bằng heartbeat.

### 2.2. Dành cho quản trị viên (Admin Dashboard)

- Xem dashboard tổng quan về phiên Wi-Fi, khách hàng, thời lượng truy cập và các số liệu phân tích như giờ cao điểm.
- Quản lý và xem danh sách khách hàng, phiên Wi-Fi và thiết bị đã ghi nhận.
- Tạo, xem và redeem voucher; theo dõi số lượt sử dụng.
- Tạo và quản lý email campaign; gửi email cho nhóm khách đã đồng ý nhận tin qua Brevo.
- Đọc file CSV và gửi email marketing qua Gmail SMTP từ trang Admin.

## 3. Công nghệ sử dụng

| Công nghệ | Vai trò |
|---|---|
| Next.js 14 (App Router) | Ứng dụng web, Server Components và API Routes |
| React 18 | Xây dựng giao diện tương tác |
| TypeScript | Kiểm tra kiểu dữ liệu |
| Tailwind CSS | Giao diện và responsive styling |
| shadcn/ui, Radix UI | Các thành phần giao diện dùng chung |
| Supabase | PostgreSQL, xác thực người dùng và truy cập dữ liệu |
| `@supabase/ssr` | Quản lý Supabase session/cookies trong Next.js |
| Zod | Kiểm tra dữ liệu đầu vào của form và API |
| Brevo | API gửi và quản lý email campaign |
| Nodemailer | Gửi email qua Gmail SMTP |
| Recharts | Hiển thị biểu đồ thống kê |
| PapaParse | Đọc và phân tích file CSV |
| next-intl | Hỗ trợ bản dịch tiếng Việt và tiếng Anh |

## 4. Chức năng từng file/thư mục (Cấu trúc mã nguồn)

```text
katinat-guest-wifi/
├── app/
│   ├── (portal)/           # Giao diện Captive Portal cho khách (/portal) và điều hướng đăng nhập
│   ├── admin/              # Giao diện cho quản trị viên (dashboard, analytics, customers, sessions, devices, vouchers, campaigns, send-email)
│   ├── api/                # API Routes (auth callback, sessions, profile, consent, vouchers, campaigns, send-smtp, export-csv, unsubscribe)
│   └── layout.tsx          # Root Layout, global styles và xử lý lỗi cấp ứng dụng
├── components/             # UI components chia theo khu vực: portal/, admin/, dashboard/, charts/ và ui/ (shadcn/ui)
├── hooks/                  # Custom React hooks (quản lý session heartbeat và ngắt kết nối)
├── lib/                    # Nơi xử lý logic chính: kết nối database Supabase, kiểm tra quyền admin, gửi email (Brevo/Gmail) và kiểm tra dữ liệu đầu vào
├── locales/                # File ngôn ngữ tiếng Việt (vi.json) và tiếng Anh (en.json)
├── public/                 # Chứa hình ảnh hiển thị trên trang đăng nhập Wi-Fi
├── scripts/                # Script hỗ trợ chụp ảnh giao diện tự động
├── supabase/migrations/    # Các file lệnh SQL dùng để tạo bảng, cấu trúc dữ liệu và dữ liệu mẫu trên Supabase
├── types/                  #  Khai báo kiểu dữ liệu dùng chung để tránh lỗi code trong toàn dự án
└── middleware.ts           # Làm mới session Supabase và phân quyền truy cập /admin
```

### Luồng kết nối chính

1. Guest Portal được render từ `app/(portal)/portal/page.tsx` và sử dụng các UI component trong `components/portal/`.
2. `middleware.ts` kiểm tra Supabase session và vai trò người dùng trước khi cho phép truy cập khu vực Admin.
3. Các trang Admin lấy dữ liệu từ `lib/services/`; những service này kết nối Supabase thông qua `lib/supabase/server.ts`.
4. Các thao tác và API được xử lý trong `app/api/`. Schema dữ liệu đầu vào được tập trung tại `lib/validations/index.ts`.
5. Cấu trúc dữ liệu và các hàm SQL được định nghĩa trong `supabase/migrations/`; TypeScript types nằm tại `types/index.ts`.

## 5. Hướng dẫn cài đặt (Local Development)

### 5.1. Yêu cầu hệ thống

- Node.js 18 trở lên.
- npm hoặc pnpm.
- Tài khoản Supabase và project đã tạo.
- Google/Facebook OAuth provider được cấu hình trong Supabase nếu muốn kiểm thử đăng nhập mạng xã hội.

### 5.2. Clone dự án và cài dependencies

```bash
git clone <URL_REPOSITORY>
cd <TEN_THU_MUC_DU_AN>
npm install
```

Repository khai báo `pnpm@9.15.4` trong `package.json`; có thể dùng pnpm thay cho npm nếu muốn đồng nhất package manager:

```bash
corepack enable
pnpm install
```

### 5.3. Tạo file `.env.local`

Tạo file `.env.local` ở thư mục gốc. Điền giá trị phù hợp từ Supabase và Brevo; không đưa file này lên GitHub.

```dotenv
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_CAFE_NAME=
NEXT_PUBLIC_CAFE_ADDRESS=
BREVO_API_KEY=
BREVO_SENDER_EMAIL=
```

`SUPABASE_SERVICE_ROLE_KEY` là secret chỉ dùng phía server; không đặt tiền tố `NEXT_PUBLIC_` cho key này và không chia sẻ công khai. Gmail SMTP được cấu hình bằng email người gửi và Gmail App Password nhập trong trang Admin, không cần ghi các thông tin đó vào repository.

### 5.4. Chạy database migrations

Trong Supabase Dashboard, mở **SQL Editor**, sau đó chạy các file trong `supabase/migrations/` theo thứ tự phiên bản tăng dần. Các file migration tạo bảng, view, RPC và index mà ứng dụng sử dụng.

Sau khi chạy migration, cấu hình Google/Facebook OAuth provider và danh sách Redirect URLs trong Supabase Auth. Callback URL local của ứng dụng:

```text
http://localhost:3000/api/auth/callback
```

Để vào Admin, tài khoản cần có `role = 'admin'` trong bảng `profiles`.

### 5.5. Chạy ứng dụng

```bash
npm run dev
```

Mở [http://localhost:3000](http://localhost:3000). Guest Portal nằm tại `/portal`, Admin Dashboard tại `/admin`.

Các lệnh kiểm tra và build:

```bash
npm run typecheck
npm run lint
npm run build
npm run start
```
