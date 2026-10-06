# BÁO CÁO LAB 1: TRẢI NGHIỆM DEVOPS WORKFLOW END-TO-END

- **Học phần:** Vận hành & Bảo trì Phần mềm (DevOps 2026)
- **Repository GitHub:** [https://github.com/lecuong2512/devops-lab1-portfolio](https://github.com/lecuong2512/devops-lab1-portfolio)
- **Website GitHub Pages:** [https://lecuong2512.github.io/devops-lab1-portfolio/](https://lecuong2512.github.io/devops-lab1-portfolio/)

---

## 1. Cấu trúc thư mục dự án

```text
devops-lab1-portfolio/
├── .github/
│   └── workflows/
│       └── deploy.yml      # CI/CD Pipeline definition (kèm Lighthouse CI)
├── index.html              # Trang chủ Portfolio
├── about.html              # Trang giới thiệu (Multi-page)
├── contact.html            # Trang liên hệ (Multi-page)
├── avatar.png              # Ảnh đại diện cá nhân
├── lighthouserc.json       # File cấu hình Google Lighthouse CI
├── README.md               # Mô tả dự án
└── BAO_CAO_LAB1.md         # Báo cáo tổng kết Lab 1
```

---

## 2. CI/CD Pipeline (`.github/workflows/deploy.yml`)

Workflow được kích hoạt tự động mỗi khi có sự kiện `push` lên nhánh `main` hoặc kích hoạt thủ công qua `workflow_dispatch`. Pipeline gồm 4 bước chính:
1. **Checkout Code:** Sử dụng `actions/checkout@v4` để lấy mã nguồn mới nhất từ kho lưu trữ.
2. **Validate Multi-page HTML & Assets:** Kiểm tra tự động sự tồn tại và tính hợp lệ của `index.html`, `about.html`, `contact.html` và `avatar.png`.
3. **Audit with Lighthouse CI:** Tích hợp `treosh/lighthouse-ci-action@v12` tự động kiểm tra chất lượng website theo 4 tiêu chuẩn của Google: Performance, Accessibility, Best Practices và SEO.
4. **Deploy to GitHub Pages:** Tự động đẩy mã nguồn lên nhánh `gh-pages` bằng action `peaceiris/actions-gh-pages@v3`.

---

## 3. Bảng so sánh DevOps vs Cách làm truyền thống (Step 4.1)

| Tiêu chí | Cách THỦ CÔNG (Traditional) | Cách DEVOPS (CI/CD Pipeline) |
|---|---|---|
| **Làm sao để deploy?** | Tải code, mở FTP/SSH kết nối server, upload file đè thủ công | `git push` lên GitHub → pipeline tự động kích hoạt |
| **Mất bao lâu?** | 15 - 30 phút mỗi lần | ~10 - 30 giây |
| **Ai làm deploy?** | Con người (Lập trình viên / SysAdmin thực hiện bằng tay) | Máy tự làm tự động qua GitHub Actions Runner |
| **Có bị sai sót không?** | Dễ sai sót (quên file, nhầm thư mục, sai phân quyền) | Không — quy trình nhất quán 100% theo workflow định sẵn |
| **Rollback nếu lỗi?** | Tìm backup file cũ upload đè lại, tốn thời gian và rủi ro | `git revert <commit>` + push → pipeline tự rollback |
| **Làm sao biết deploy thành công?** | Mở trình duyệt F5 nhiều lần, tự check log server | GitHub Actions thông báo trực quan trạng thái (✅ Pass / ❌ Fail) |

---

## 4. Sơ đồ DevOps Workflow (Step 4.2)

```text
  ┌────────────────┐       git commit        ┌─────────────────┐
  │   Lập trình    │ ──────────────────────► │ Nhánh 'main'    │
  │ (HTML, CSS, JS)│        & git push       │ trên GitHub     │
  └────────────────┘                         └────────┬────────┘
                                                      │
                                                      ▼ (Webhook Trigger)
                                             ┌─────────────────┐
                                             │ GitHub Actions  │
                                             │ CI/CD Pipeline  │
                                             └────────┬────────┘
                                                      │
      ┌─────────────────────┬─────────────────────────┼─────────────────────────┐
      │                     │                         │                         │
      ▼                     ▼                         ▼                         ▼
 [1. Checkout]     [2. Validate Files]     [3. Lighthouse CI Audit]      [4. Auto Deploy]
(actions/checkout)  (index, about, contact) (Perf, A11y, Best, SEO)   (peaceiris/actions-gh-pages)
                                                                                │
                                                                                ▼
                                                                      ┌─────────────────┐
                                                                      │ Nhánh 'gh-pages'│
                                                                      └────────┬────────┘
                                                                               │
                                                                               ▼
                                                                      ┌─────────────────┐
                                                                      │  GitHub Pages   │
                                                                      │  Website LIVE!  │
                                                                      └─────────────────┘
```

---

## 5. Trả lời câu hỏi thu hoạch (Step 4.3)

1. **DevOps workflow tự động hóa những bước nào mà cách thủ công phải làm bằng tay?**
   - Tự động hóa quá trình lấy mã nguồn (Checkout), kiểm thử/xác thực tính toàn vẹn của mã nguồn (Validate), audit chất lượng website tự động (Lighthouse CI), đóng gói và triển khai (Deploy) trực tiếp lên máy chủ hosting mà không cần sự can thiệp thủ công của con người.

2. **Nếu bạn deploy sai (website bị lỗi), làm sao để quay lại phiên bản cũ?**
   - Chỉ cần sử dụng lệnh `git revert <commit-bị-lỗi>` (hoặc reset về commit ổn định) rồi push lên nhánh `main`. Pipeline CI/CD sẽ nhận diện commit mới và tự động triển khai phiên bản ổn định trước đó trong vòng vài giây.

3. **GitHub Actions pipeline đã giúp bạn tiết kiệm bao nhiêu thời gian so với cách thủ công?**
   - Quy trình thủ công mất từ 15 đến 30 phút cho mỗi đợt triển khai (đăng nhập máy chủ, FTP tải file, phân quyền, kiểm tra). Với GitHub Actions, thao tác chỉ gói gọn trong 1 lệnh `git push` (chưa đến 10 giây), toàn bộ quy trình còn lại máy chủ tự động xử lý.

---

## 6. Bài tập mở rộng đã hoàn thành

1. **Thêm ảnh đại diện (Avatar):**
   - Đã tích hợp ảnh avatar cá nhân `avatar.png` bo tròn, viền gradient hiện đại trên cả 3 trang.
2. **Hệ thống đa trang (Multi-page & Navigation):**
   - Tạo thêm `about.html` (Giới thiệu bản thân, mục tiêu học phần) và `contact.html` (Form liên hệ và link mạng xã hội).
   - Thanh điều hướng `<nav class="nav">` kết nối linh hoạt, đồng bộ giao diện trên cả 3 trang.
3. **Tích hợp Google Lighthouse CI (LHCI):**
   - Tích hợp `treosh/lighthouse-ci-action@v12` kết hợp cấu hình `lighthouserc.json`.
   - Mỗi lần push code, hệ thống tự động kiểm thử tiêu chuẩn Performance, Accessibility, Best Practices và SEO, đồng thời lưu trữ báo cáo audit artifact trực tiếp trên GitHub Actions.
