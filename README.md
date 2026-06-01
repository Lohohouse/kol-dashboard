# KOL TikTok Dashboard - LOHO House

Dashboard công khai báo cáo doanh thu KOL theo tháng / theo ngày / theo KOL.

**Link công khai sau khi deploy:** `https://<username-công-ty>.github.io/kol-dashboard/`

Nguồn dữ liệu: Google Sheets `KOL_TikTok_Database_PerMonth`

---

## Cấu trúc file

| File | Mô tả |
|---|---|
| `index.html` | Dashboard (GitHub Pages serve file này) |
| `kol_dashboard_template.html` | Khung HTML — giao diện + logic JS |
| `update_kol_dashboard.py` | Script tải Sheets → generate `index.html` |
| `.github/workflows/update.yml` | Lịch chạy auto-update (mặc định mỗi giờ) |
| `README.md` | Hướng dẫn này |

---

## Setup ban đầu (chỉ làm 1 lần)

### Bước 1: Tạo repo trong GitHub công ty
1. Đăng nhập GitHub bằng account/org của công ty
2. **New repository** → tên `kol-dashboard` → chọn **Public** → Create
3. Giải nén folder `kol-dashboard-loho` này
4. Upload toàn bộ files lên repo qua **Add file → Upload files** (kéo thả)
5. Folder `.github/workflows/update.yml` upload riêng bằng **Add file → Create new file** với tên `.github/workflows/update.yml`

### Bước 2: Bật GitHub Pages
- Settings → Pages
- Source: **Deploy from a branch** · Branch: **main** / Folder: **/ (root)** → Save
- Đợi 1-2 phút → link công khai hiện ra ở đầu trang Pages

### Bước 3: Bật quyền cho GitHub Actions
- Settings → **Actions → General** → cuộn xuống cuối
- **Workflow permissions** → chọn **"Read and write permissions"** → Save

### Bước 4: Public Google Sheets
- Mở Sheets `KOL_TikTok_Database_PerMonth`
- Share → đổi "General access" → **Anyone with the link** · Viewer

### Bước 5: Test chạy workflow
- Tab **Actions** → click `Update KOL Dashboard` → **Run workflow**
- Đợi 30-60s → ✅ xanh = OK
- Mở link công khai → Ctrl+F5 → kiểm tra dòng "Cập nhật" có giờ mới nhất

---

## Cách thêm dữ liệu tháng mới

1. Mở Google Sheets `KOL_TikTok_Database_PerMonth`
2. Tạo sheet mới tên đúng format: `T6_DonHang`, `T7_DonHang`, ...
   - **Lưu ý quan trọng:** tắt unikey trước khi gõ tên sheet để tránh dấu thừa (vd `T̀6_DonHang` sai)
   - Cấu trúc cột giống các tháng trước
3. Đợi tối đa 1 giờ → dashboard tự update
4. Hoặc update ngay: Actions → Run workflow

---

## Đổi lịch cập nhật

Sửa dòng cron trong `.github/workflows/update.yml`:

| Lịch | Cron |
|---|---|
| Mỗi giờ (mặc định) | `0 * * * *` |
| Mỗi 30 phút | `*/30 * * * *` |
| 2 lần/ngày 8h-16h VN | `0 1,9 * * *` |
| 1 lần/ngày 8h VN | `0 1 * * *` |

GitHub free có giới hạn 2.000 phút Actions/tháng. Lịch mỗi giờ dùng ~240 phút/tháng → an toàn.

---

## Tính năng dashboard

### 4 tab chính
- **總覽 Tổng Quan**: KPI tổng, bảng tăng trưởng tháng có filter loại nội dung, chart so sánh GMV/đơn các tháng, Top 15 KOL, phân tích loại nội dung, Top SP
- **📅 Theo Tháng**: dropdown chọn tháng → xem chi tiết tháng (KPI có ▲▼, daily chart, Top 10 KOL kèm SP bán chạy)
- **🔍 Tra Cứu Theo Ngày**: chọn khoảng ngày tùy ý → KPI + so sánh 3 kỳ + Top 10 SP + Top 10 KOL kỳ này / kỳ trước + bảng KOL so sánh cùng kỳ tháng trước (có date picker riêng)
- **達人 KOL**: toàn bộ KOL có phân trang, mỗi KOL kèm SP bán chạy nhất + AOV

### Bộ lọc chung
- 🏷️ Trạng thái đơn (Đã quyết toán / Không đủ điều kiện / Chờ xử lý / Khách hàng chưa thanh toán)
- 📅 Khoảng ngày

### Logic gom sản phẩm
- "Combo sàn dán (5m² + 1m²)" — gom tất cả combo sàn nhựa
- "Sơn tường 1kg (tất cả tone)" — gom mọi tone màu
- Còn lại giữ nguyên tên
