# Katinat Captive Portal (Nhóm 8)

> **Deploy dụ án qua Vercel :** [https://ueh-katinat-captive-portal.vercel.app/portal](https://ueh-katinat-captive-portal.vercel.app/portal)

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
│   ├── (portal)/                         # Trang đăng nhập Wi-Fi và giao diện dành cho khách hàng
│   ├── admin/                            # Trang quản trị (admin): xem thống kê, quản lý khách, phiên Wi-Fi, voucher và gửi email
│   ├── api/                              # Nơi xử lý các tác vụ ngầm: đăng nhập, lưu phiên kết nối, gửi email và đổi voucher
│   ├── layout.tsx                        # Khung giao diện chung cho toàn bộ trang web
│   └── globals.css                       # Định dạng giao diện (CSS) áp dụng chung cho toàn dự án
├── components/
│   ├── admin/                            # Các khối giao diện dùng riêng cho trang quản trị (bảng dữ liệu, form nhập...)
│   ├── portal/                           # Các khối giao diện dùng cho trang Wi-Fi của khách
│   ├── charts/                           # Các loại biểu đồ thống kê doanh thu, lượt truy cập
│   └── ui/                               # Các thành phần giao diện cơ bản dùng chung (nút bấm, ô nhập, hộp thoại...)
├── hooks/                                # Hàm hỗ trợ theo dõi trạng thái kết nối Wi-Fi và tương tác giao diện
├── lib/
│   ├── services/                         # Xử lý logic chính và lấy dữ liệu từ database lên giao diện
│   ├── supabase/                         # Thiết lập kết nối với cơ sở dữ liệu Supabase (cho trình duyệt, server và quyền admin)
│   ├── validations/index.ts              # Kiểm tra tính hợp lệ của dữ liệu trước khi lưu (email, form, voucher...)
│   ├── auth/                             # Kiểm tra tài khoản đăng nhập và phân quyền Admin
│   ├── email/                            # Tạo mẫu email (template) và xử lý nội dung gửi mail
│   └── utils.ts                          # Các hàm tiện ích nhỏ dùng chung ở nhiều nơi
├── locales/                              # Bộ từ điển dịch giao diện sang tiếng Việt và tiếng Anh
├── public/                               # Nơi lưu trữ hình ảnh, logo và các tệp tĩnh
├── supabase/migrations/                  # Các tệp lệnh SQL dùng để tạo bảng và cấu trúc cơ sở dữ liệu
├── types/index.ts                        # Khai báo định dạng dữ liệu dùng chung để tránh sai sót khi lập trình
├── middleware.ts                         # "Người gác cổng": giữ trạng thái đăng nhập và chặn người lạ vào trang /admin
├── next.config.js                        # Tệp cài đặt và cấu hình hoạt động cho Next.js
└── package.json                          # Danh sách các thư viện cài đặt và các câu lệnh chạy dự án
```

### Luồng kết nối chính

1. Guest Portal được render từ `app/(portal)/portal/page.tsx` và sử dụng các UI component trong `components/portal/`.
2. `middleware.ts` kiểm tra Supabase session và vai trò người dùng trước khi cho phép truy cập khu vực Admin.
3. Các trang Admin lấy dữ liệu từ `lib/services/`; những service này kết nối Supabase thông qua `lib/supabase/server.ts`.
4. Các thao tác và API được xử lý trong `app/api/`. Schema dữ liệu đầu vào được tập trung tại `lib/validations/index.ts`.
5. Cấu trúc dữ liệu và các hàm SQL được định nghĩa trong `supabase/migrations/`; TypeScript types nằm tại `types/index.ts`.

## 5. Hướng dẫn cài đặt ở máy Local 

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
