# UNIVERSAL BYPASS TOOL — Dark Edition

Bộ mã nguồn gồm:
- `index.html`: giao diện tĩnh, chạy trên GitHub Pages.
- `cloudflare-worker/worker.js`: API phân tích chuỗi chuyển hướng HTTP.
- `cloudflare-worker/wrangler.toml`: cấu hình deploy Cloudflare Workers.

## Giới hạn quan trọng
Tên “Auto Bypass” trong dự án này có nghĩa là tự động theo các chuyển hướng HTTP công khai (301, 302, 303, 307, 308). Công cụ không vượt CAPTCHA, paywall, đăng nhập, quảng cáo bắt buộc, mã xác minh, JavaScript challenge hoặc cơ chế kiểm soát truy cập. Một số dịch vụ có thể chặn request từ máy chủ hoặc yêu cầu thao tác trên trình duyệt; khi đó công cụ sẽ báo giới hạn thay vì giả vờ đã vượt link.

## 1) Đưa giao diện lên GitHub Pages
1. Tạo repository GitHub mới.
2. Upload `index.html` ở thư mục gốc.
3. Vào Settings → Pages → Deploy from a branch → chọn `main` và `/ (root)`.
4. Lưu URL Pages, ví dụ `https://USERNAME.github.io/REPOSITORY/`.

## 2) Deploy API lên Cloudflare Workers
Cần Node.js và Wrangler:
```bash
cd cloudflare-worker
npm install -g wrangler
wrangler login
wrangler deploy
```
Sau khi deploy, ghi lại URL Worker, dạng `https://TEN-WORKER.TEN-TAI-KHOAN.workers.dev`.

## 3) Cấu hình CORS
Trong Cloudflare Dashboard → Workers & Pages → Worker → Settings → Variables, tạo biến:
- Name: `ALLOWED_ORIGINS`
- Value: URL GitHub Pages chính xác, ví dụ `https://USERNAME.github.io`

Nếu dùng custom domain thì thêm origin đó, phân cách bằng dấu phẩy. Tránh dùng `*` trong triển khai công khai.

## 4) Kết nối giao diện với API
Mở website GitHub Pages, dán URL Worker vào ô **CLOUDFLARE WORKER API**. Cấu hình được lưu trong trình duyệt trên thiết bị đó.

## 5) Kiểm tra
Mở `https://TEN-WORKER.TEN-TAI-KHOAN.workers.dev/health`; kết quả mong đợi:
```json
{"ok":true,"service":"Universal Link Resolver API"}
```
Sau đó thử một URL chuyển hướng công khai mà bạn tin cậy.

## Bảo mật và vận hành
- Worker chặn một số địa chỉ IPv4/IPv6 nội bộ, localhost và metadata endpoint; không coi đây là biện pháp chống SSRF tuyệt đối.
- Không dùng để xử lý URL riêng tư, URL có token đăng nhập hoặc dữ liệu nhạy cảm.
- Trước khi public rộng rãi, bật Cloudflare rate limiting/WAF, theo dõi log và giới hạn lưu lượng.
- GitHub Pages chỉ host giao diện tĩnh; phần phân tích chuyển hướng cần Worker hoặc backend tương đương.
- Công cụ chỉ hiển thị URL đích để người dùng tự kiểm tra; không tự động mở URL đích.

## Cấu trúc
```text
universal-bypass-tool/
├── index.html
├── README.md
└── cloudflare-worker/
    ├── worker.js
    └── wrangler.toml
```
