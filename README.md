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
│   ├── (portal)/                         # Route Group dành cho trang khách; không tạo tiền tố URL
│   │   ├── login/page.tsx                # Chuyển hướng trang login cũ sang Portal
│   │   ├── page.tsx                      # Chuyển hướng trang gốc sang /portal
│   │   └── portal/page.tsx               # Trang Captive Portal dành cho khách
│   ├── admin/                            # Giao diện quản trị
│   │   ├── analytics/                    # Biểu đồ và số liệu phân tích
│   │   ├── campaigns/                    # Danh sách, tạo mới và chi tiết campaign
│   │   ├── customers/                    # Danh sách và chi tiết khách hàng
│   │   ├── dashboard/                    # Các chỉ số tổng quan
│   │   ├── devices/                      # Danh sách thiết bị
│   │   ├── send-email/                   # Form gửi email marketing qua SMTP
│   │   ├── sessions/                     # Danh sách phiên Wi-Fi
│   │   ├── vouchers/                     # Quản lý, redeem và xem chi tiết voucher
│   │   ├── error.tsx                     # Error Boundary của khu vực Admin
│   │   ├── layout.tsx                    # Bố cục chung cho Admin
│   │   └── loading.tsx                   # Trạng thái tải của khu vực Admin
│   ├── api/                              # API Routes của ứng dụng
│   │   ├── admin/export-csv/             # Xuất dữ liệu khách hàng CSV
│   │   ├── auth/callback/                 # Xử lý callback OAuth
│   │   ├── campaigns/                    # API campaign và gửi campaign
│   │   ├── consent/                      # Ghi nhận consent nhận marketing
│   │   ├── profile/                      # Cập nhật hồ sơ khách
│   │   ├── send-smtp/                    # Gửi email qua Gmail SMTP
│   │   ├── sessions/                     # Tạo, heartbeat và kết thúc session
│   │   ├── unsubscribe/                  # Xử lý hủy đăng ký email
│   │   └── vouchers/                     # Tạo, cập nhật, campaign và redeem voucher
│   ├── error.tsx                         # Error Boundary cấp ứng dụng
│   ├── global-error.tsx                 # Xử lý lỗi ở Root Layout
│   ├── globals.css                      # Global styles và Tailwind directives
│   └── layout.tsx                       # Root Layout, locale provider và thông báo
├── components/
│   ├── admin/                            # Bảng, form và thao tác của Admin
│   ├── charts/                           # Biểu đồ dashboard và analytics
│   ├── dashboard/                       # Thẻ thống kê và bộ lọc thời gian
│   ├── portal/                           # Login, consent, hồ sơ và luồng Portal
│   └── ui/                               # Thành phần UI dùng chung (shadcn/ui)
├── hooks/                                # React hooks, gồm session heartbeat/end
├── lib/
│   ├── auth/                             # Lấy user hiện tại và kiểm tra quyền Admin
│   ├── brevo/                            # Khởi tạo Brevo client
│   ├── email/                            # Chuẩn hóa HTML, unsubscribe và email template
│   ├── i18n/                             # Cấu hình next-intl
│   ├── network/                          # Tạo fingerprint và nhận diện thiết bị
│   ├── services/                         # Truy vấn analytics, customer, session, voucher, campaign
│   ├── supabase/
│   │   ├── admin.ts                     # Supabase client server-side có quyền admin
│   │   ├── client.ts                    # Supabase client phía trình duyệt
│   │   └── server.ts                    # Supabase client phía server với cookies
│   ├── validations/
│   │   └── index.ts                     # Zod schema cho campaign và voucher
│   └── utils.ts                         # Hàm tiện ích chung, ví dụ cn()
├── locales/
│   ├── en.json                           # Chuỗi giao diện tiếng Anh
│   └── vi.json                           # Chuỗi giao diện tiếng Việt
├── public/
│   ├── images/portal/                    # Hình ảnh dùng trong Guest Portal
│   └── report_images/                    # Ảnh chụp giao diện phục vụ báo cáo
├── scripts/
│   └── capture-report-screenshots.mjs    # Script chụp ảnh giao diện báo cáo
├── supabase/
│   └── migrations/                       # SQL migrations: schema, seed, RPC và index
├── types/
│   └── index.ts                          # Database typing và domain types dùng chung
├── middleware.ts                         # Làm mới session và bảo vệ đường dẫn /admin
├── next.config.js                        # Cấu hình Next.js và next-intl
├── package.json                          # Dependencies và npm scripts
├── tailwind.config.ts                    # Cấu hình Tailwind CSS
└── tsconfig.json                         # TypeScript và alias @/
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
