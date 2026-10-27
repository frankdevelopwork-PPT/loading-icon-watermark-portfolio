# FRANK DEVELOP kit — hướng dẫn sử dụng

Bộ thành phần dùng lại cho mọi trang web: **spinner Hồn Hoàn** (loading), **huy hiệu** và **bộ icon**. Mỗi file JS tự chứa mọi thứ, không cần thư viện hay CDN nào khác.

Bản build: 2026-10-27 · © 2026 FRANK DEVELOP. Xem `LICENSE.txt` trước khi dùng.

## Các file

| File | Là gì | Dung lượng |
|---|---|---|
| `frank-seal-spinner-2d.js` | Spinner, bản 2D (vector SVG) | 62 kB (21 kB gzip) |
| `frank-seal-spinner.js` | Spinner, bản ảnh (vòng render từ cảnh 3D) | 519 kB (377 kB gzip) |
| `frank-seal-spinner-3d.js` | Spinner, bản 3D (WebGL) | 1,08 MB (274 kB gzip) |
| `frank-badge-2d.js` | Huy hiệu, bản 2D | 58 kB (19 kB gzip) |
| `frank-badge.js` | Huy hiệu, bản ảnh | 244 kB (172 kB gzip) |
| `icons/`, `site.webmanifest` | Favicon (SVG, ICO, PNG 16/32), icon iPhone 180, Android 192/512 và maskable | |
| `demo.html`, `playground.html` | Trang mẫu và phòng thử | |
| `LICENSE.txt`, `THIRD-PARTY-NOTICES.txt` | Giấy phép (phải đi kèm khi phát hành) | |

Mỗi file JS có thêm bản `.mjs` cho dự án dùng bundler (`import { spinner, overlay } from './frank-seal-spinner.mjs'`).

## Chọn bản nào

| Bản | Spinner | Huy hiệu | Khi nào dùng |
|---|---|---|---|
| **2D** (vector) | `frank-seal-spinner-2d.js` | `frank-badge-2d.js` | Nhẹ nhất, hiện gần như tức thì. Mặc định nên dùng |
| **Ảnh** | `frank-seal-spinner.js` | `frank-badge.js` | Đẹp hơn (vòng render từ cảnh 3D, có quầng sáng), file nặng hơn |
| **3D** (WebGL) | `frank-seal-spinner-3d.js` | (dùng kèm huy hiệu 2D hoặc ảnh) | Đẹp nhất, nặng, tốn GPU. Chỉ một cái mỗi trang, hợp làm màn chờ cả trang |

Cả ba bản spinner cùng thẻ HTML và cùng lệnh JS: đổi bản chỉ cần đổi tên file. **Mỗi trang chỉ nạp một bản spinner** (bản nạp trước được dùng).

## Tên miền được phép

Spinner và huy hiệu chỉ hiển thị bình thường trên các tên miền sau (gồm cả tên miền con), và khi mở file trực tiếp trên máy (`file://`):

- `frankdevelopwork-ppt.github.io`
- `localhost`
- `127.0.0.1`

Trên tên miền khác chúng vẫn chạy, nhưng chuyển xám và hiện "© FRANK DEVELOP". Muốn dùng trên trang mới, liên hệ FRANK DEVELOP để thêm tên miền và nhận bản build mới.

## 1. Loading cả trang

Đặt ngay đầu `<body>`. Lớp phủ tự biến mất khi trang tải xong:

```html
<script src="frank-seal-spinner-2d.js" data-auto></script>
```

Tùy chọn trên thẻ `<script>`:

| Thuộc tính | Tác dụng |
|---|---|
| `data-min-duration="1500"` | Hiện ít nhất 1,5 giây (trang tải quá nhanh cũng không bị chớp) |
| `data-manual` | Không tự tắt; tự gọi `FrankSealOverlay.hide()` khi xong |
| `data-background="#fff"` | Màu nền lớp phủ (mặc định đen tím `#05040a`) |
| `data-backdrop="dark"` | Thêm mề đay tròn tối sau con dấu (hợp với nền sáng) |

Tự điều khiển bằng code:

```js
const cover = FrankSeal.overlay({ minDuration: 800 });
await loadEverything();
await cover.hide();   // các vòng giảm tốc, dừng, logo chớp sáng, lớp phủ mờ đi
```

## 2. Loading trong một khung, thẻ, nút

```html
<script src="frank-seal-spinner-2d.js"></script>

<!-- cỡ cố định -->
<frank-seal-spinner size="96"></frank-seal-spinner>

<!-- phủ kín một khung (khung cần position: relative) -->
<div style="position: relative; height: 300px">
  <frank-seal-spinner size="fill"></frank-seal-spinner>
</div>
```

```js
// phủ kín #panel trong lúc tải dữ liệu
const s = FrankSeal.spinner(document.querySelector('#panel'));
try {
  const data = await fetch('/api/data').then((r) => r.json());
  // … hiển thị dữ liệu …
} finally {
  await s.done({ hide: true });   // dừng, chớp sáng, mờ đi, tự gỡ
}

// spinner nhỏ trong nút
const btn = document.querySelector('#save');
const mini = FrankSeal.spinner(btn, { size: 20, backdrop: 'none' });
btn.prepend(mini);
// … xong việc: await mini.done({ hide: true });
```

Thuộc tính của `<frank-seal-spinner>`:

| Thuộc tính | Tác dụng |
|---|---|
| `size` | Số px, hoặc `fill` để phủ kín khung cha |
| `backdrop` | `dark` (mặc định): mề đay tròn tối, đọc rõ trên nền sáng · `none`: bỏ, hợp nền tối |
| `speed` | Hệ số tốc độ quay (mặc định 1) |
| `label` | Tên đọc cho trình đọc màn hình (mặc định "Đang tải") |
| `loading="false"` | Đặt vào là spinner dừng (giống gọi `done()`) |
| `lightning="false"` | Chỉ bản 3D: tắt sét |

Phương thức và sự kiện: `done()` (dừng, trả về Promise khi đã dừng hẳn), `done({ hide: true })` (dừng rồi tự gỡ), sự kiện `frank-done` khi dừng hẳn.

## 3. Huy hiệu

```html
<script src="frank-badge-2d.js" defer></script>
<frank-badge href="https://trang-cua-ban.com" target="_blank" position="bottom-right" size="96"></frank-badge>
```

| Thuộc tính | Tác dụng |
|---|---|
| `position` | `bottom-right`, `bottom-left`, `top-right`, `top-left`: cố định ở góc. Bỏ trống: nằm trong dòng chữ |
| `size` | Đường kính px (mặc định 96; từ ~120 px đọc rõ chữ logo) |
| `href`, `target` | Liên kết khi bấm. Bỏ `href` thì huy hiệu chỉ là hình |
| `label` | Tên đọc cho trình đọc màn hình (mặc định "FRANK DEVELOP") |

Rê chuột hoặc focus bằng bàn phím: huy hiệu nổi lên, có quầng sáng ngọc, các vòng quay nhanh hơn.

## 4. Icon trang (favicon)

Chép thư mục `icons/` và file `site.webmanifest` vào gốc trang web, rồi thêm vào `<head>`:

```html
<link rel="icon" href="/icons/favicon.ico" sizes="32x32">
<link rel="icon" href="/icons/favicon.svg" type="image/svg+xml">
<link rel="apple-touch-icon" href="/icons/apple-touch-icon.png">
<link rel="manifest" href="/site.webmanifest">
<meta name="theme-color" content="#05040a">
```

## Dùng từ một địa chỉ chung (CDN, GitHub Pages)

Nếu bộ kit được đặt trên một địa chỉ web cố định, trang nào cũng chỉ cần trỏ tới đó:

```html
<script src="https://<địa-chỉ-kit>/v1/frank-seal-spinner-2d.js" data-auto></script>
<script src="https://<địa-chỉ-kit>/v1/frank-badge-2d.js" defer></script>
```

- Nên đặt mỗi bản phát hành trong một thư mục có số phiên bản (`v1/`, `v2/`…): cập nhật kit không làm hỏng các trang đang dùng bản cũ.
- Tên miền của **trang đang dùng kit** phải nằm trong danh sách được phép ở trên (nơi đặt file thì không cần).
- Trang dùng kit cần có mạng để tải file. Icon (mục 4) nên chép vào từng trang.

## Xem thử

- `demo.html`: các cách dùng (lớp phủ, nhiều cỡ, trong nút, trong thẻ, huy hiệu). Thêm `?2d` hoặc `?3d` để xem bản khác.
- `playground.html`: chọn 3D / Ảnh / 2D hoặc so sánh cả 3, chỉnh cỡ, nền, tốc độ, đo FPS và thời gian tải.

Bấm đúp mở thẳng từ ổ đĩa được (mở file trên máy đang được phép).

## Lưu ý

- Cần trình duyệt hiện đại (Chrome, Edge, Firefox, Safari bản gần đây). Bản 3D cần WebGL 2.
- Người dùng bật "giảm chuyển động" (`prefers-reduced-motion`) sẽ thấy spinner quay chậm, không có hiệu ứng tăng tốc.
- Không sửa nội dung các file JS: mã đã được làm rối và tự bảo vệ, sửa vào sẽ làm hỏng file. Dòng bản quyền và `LICENSE.txt`, `THIRD-PARTY-NOTICES.txt` phải đi kèm khi phát hành.
