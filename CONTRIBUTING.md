# Hướng Dẫn Đóng Góp Mã Nguồn (Contributing Guide) - OmniLife Personal OS

Tài liệu này quy định quy trình làm việc với Git, chiến lược phân nhánh, tiêu chuẩn viết Commit Message và quy trình tạo Pull Request cho dự án **OmniLife**. Mọi thành viên và Agent tham gia phát triển dự án bắt buộc tuân thủ nghiêm ngặt các quy tắc dưới đây.

---

## 1. Chiến Lược Phân Nhánh (Branching Strategy)

Dự án **OmniLife** áp dụng mô hình **GitHub Flow kết hợp Feature Branching** với 2 nhánh cố định và hệ thống nhánh phát triển theo từng nhiệm vụ trong [docs/roadmap.md](./docs/roadmap.md).

### 1.1. Các Nhánh Cốt Lõi (Permanent Branches)

1. **`main` (Production Branch):**
   - Nhánh sản phẩm chính thức, luôn ở trạng thái ổn định và vượt qua 100% bài kiểm thử tự động (JUnit 5 ở Backend và TypeScript/ESLint ở Frontend).
   - Tuyệt đối không commit trực tiếp lên nhánh `main`. Mã nguồn chỉ được đưa vào `main` thông qua Pull Request từ nhánh `develop` hoặc `hotfix/*`.
   - Mỗi lần hợp nhất vào `main` sẽ được gắn thẻ phiên bản theo chuẩn Semantic Versioning (`v0.1.0`, `v0.2.0`, `v1.0.0`) và kích hoạt triển khai lên Render & Vercel.

2. **`develop` (Integration Branch):**
   - Nhánh tích hợp trung tâm trong quá trình phát triển hàng ngày.
   - Mọi nhánh tính năng (`feature/*`) được tách ra từ `develop` và hợp nhất ngược về `develop` sau khi hoàn thành từng Task.

### 1.2. Quy Ước Đặt Tên Nhánh Tạm Thời (Supporting Branches)

Sử dụng chữ thường, nối bằng dấu gạch ngang (`kebab-case`) và gắn kèm mã Task từ `docs/roadmap.md`:

| Loại nhánh | Cấu trúc đặt tên | Ví dụ thực tế | Mục đích sử dụng |
| :--- | :--- | :--- | :--- |
| **Feature** | `feature/TASK-<id>-<ten-ngan-gon>` | `feature/TASK-06-urgency-scoring-engine` | Phát triển tính năng mới theo từng Task trong Roadmap |
| **Bugfix** | `fix/<mo-ta-loi>` | `fix/task-optimistic-lock-conflict` | Sửa lỗi phát sinh trong quá trình phát triển trên nhánh `develop` |
| **Hotfix** | `hotfix/<mo-ta-loi-khan-cap>` | `hotfix/google-oauth2-redirect-uri` | Sửa lỗi khẩn cấp phát hiện trên môi trường Production (`main`) |
| **Docs** | `docs/<noi-dung-tai-lieu>` | `docs/update-mysql-explain-benchmark` | Cập nhật tài liệu kiến trúc hoặc kịch bản kiểm thử SQL |
| **Refactor** | `refactor/<pham-vi-toi-uu>` | `refactor/eliminate-study-n-plus-one-query` | Tái cấu trúc mã nguồn hoặc tối ưu hóa truy vấn JPA mà không đổi nghiệp vụ |

---

## 2. Chuẩn Đặt Tên Commit (Conventional Commits v1.0.0)

Mọi commit message bắt buộc tuân thủ cấu trúc chuẩn **Conventional Commits**:

```text
<type>(<scope>): <subject>

[optional body]

[optional footer(s)]
```

### 2.1. Danh mục `<type>` hợp lệ
- `feat`: Thêm mới một tính năng, màn hình giao diện hoặc API endpoint.
- `fix`: Sửa một lỗi nghiệp vụ, lỗi giao diện hoặc lỗi truy vấn cơ sở dữ liệu.
- `refactor`: Tái cấu trúc mã nguồn (ví dụ: áp dụng Strategy Pattern, tách DTO Projection) không làm thay đổi hành vi bên ngoài.
- `perf`: Tối ưu hóa hiệu năng (ví dụ: thêm Composite Index trên MySQL, bật `@Transactional(readOnly = true)`, cấu hình Caffeine Cache).
- `test`: Thêm mới hoặc cập nhật Unit Test / Integration Test (JUnit 5, Mockito, AssertJ).
- `docs`: Cập nhật tài liệu thiết kế hoặc chú thích Javadoc / OpenAPI.
- `chore`: Cập nhật cấu hình `pom.xml`, `package.json`, Docker hoặc GitHub Actions.

### 2.2. Danh mục `<scope>` theo các phân hệ của OmniLife
- `db`: Thay đổi Flyway migration (`V1__`, `V2__`) hoặc file MySQL Workbench.
- `security`: Spring Security 6, JWT Filter, Google OAuth2.
- `user`: Quản lý hồ sơ người dùng và cấu hình cá nhân (`user_preferences`).
- `productivity`: Lịch biểu, Công việc, Subtasks, Pomodoro và Động cơ tính điểm khẩn cấp động (`UrgencyScoringEngine`).
- `hud`: Cửa sổ nổi Always-on-Top Mini HUD (Document Picture-in-Picture API).
- `study`: Học kỳ, Môn học, Trích xuất tài liệu PDF/DOCX và Thuật toán Spaced Repetition SM-2.
- `finance`: Ví tiền, Danh mục thu chi, Giao dịch sổ cái và Ngân sách tháng.
- `habit`: Thói quen, Điểm danh thể thao, chuỗi Streak và Biểu đồ nhiệt 365 ngày.
- `journal`: Nhật ký Markdown, Chỉ số cảm xúc và Bản chụp hoạt động tự động (`DailyActivitySnapshot`).
- `audio`: Trình phát nhạc Lo-Fi và bộ trộn âm thanh trắng đa luồng.

### 2.3. Quy tắc viết `<subject>`
1. Viết bằng tiếng Anh ngắn gọn, súc tích (tối đa 72 ký tự).
2. Sử dụng động từ nguyên mẫu ở thì hiện tại (`add`, `implement`, `fix`, `optimize` — không dùng `added`, `fixed`).
3. Không viết hoa chữ cái đầu tiên của `<subject>` và không đặt dấu chấm kết thúc câu ở cuối dòng.

### 2.4. Ví dụ Commit Message hợp lệ

```text
feat(productivity): implement dynamic urgency score calculation engine

- Calculate time decay factor using non-linear hyperbolic curve
- Add scheduled job running every 15 minutes to update urgency tiers
- Ref: TASK-06
```

```text
fix(finance): apply pessimistic write lock on wallet balance updates

- Prevent lost update race condition when recording concurrent transactions
- Add JUnit 5 concurrency test case with CountDownLatch
- Ref: TASK-11
```

---

## 3. Quy Trình Thực Thi Nhiệm Vụ & Tạo Pull Request

Khi bắt đầu thực hiện một Task trong `docs/roadmap.md`, hãy tuân thủ tuần tự 5 bước sau:

### Bước 1: Đồng bộ nhánh tích hợp và tạo nhánh mới
```bash
git checkout develop
git pull origin develop
git checkout -b feature/TASK-06-urgency-scoring-engine
```

### Bước 2: Cập nhật trạng thái công việc
- Mở file `docs/worklog.md` (khi được phép cập nhật tiến độ) và chuyển trạng thái của Task tương ứng từ `TODO` sang `IN_PROGRESS`.

### Bước 3: Phát triển mã nguồn và tự kiểm thử (Self-Verification)
- Tuân thủ kiến trúc Modular Monolith ở Backend và Next.js App Router ở Frontend.
- Đảm bảo không phát sinh lỗi N+1 Query trong Hibernate (sử dụng `fetch = FetchType.LAZY` kết hợp `@EntityGraph` hoặc DTO Projection).
- Chạy kiểm thử trước khi commit:
  - **Backend:** `./mvnw clean verify` (hoặc `./mvnw clean test`) tại thư mục `backend/`.
  - **Frontend:** `npm run lint && npx tsc --noEmit && npm run build` tại thư mục `frontend/`.

### Bước 4: Kiểm tra thay đổi và Commit
- Chạy `git status` và `git diff` để rà soát toàn bộ thay đổi, đảm bảo không để sót các câu lệnh ghi log tạm thời (`System.out.println`, `console.log`) hoặc thông tin nhạy cảm (`.env`).
```bash
git add .
git commit -m "feat(productivity): implement dynamic urgency scoring engine (TASK-06)"
```

### Bước 5: Đẩy nhánh và mở Pull Request
```bash
git push -u origin feature/TASK-06-urgency-scoring-engine
```
- Tạo Pull Request trên GitHub hướng về nhánh `develop`.
- Trong mô tả Pull Request, ghi rõ:
  1. Mã Task và mục tiêu thực hiện.
  2. Danh sách các file hoặc API đã thêm/sửa.
  3. Kết quả chạy kiểm thử thực tế (JUnit 5 / Build).
- Chỉ hợp nhất (Merge) khi GitHub Actions CI (`backend-build-and-test` và `frontend-lint-and-typecheck`) báo trạng thái thành công.

---

## 4. Nguyên Tắc An Toàn Bảo Mật & Git

1. **Bảo mật thông tin nhạy cảm:** Tuyệt đối không đưa file `.env`, `.env.local`, mật khẩu cơ sở dữ liệu thật, `GOOGLE_CLIENT_SECRET`, `MAIL_APP_PASSWORD` hoặc `JWT_SECRET_KEY` vào lịch sử Git. Chỉ sử dụng `.env.example` với các giá trị mẫu.
2. **Bảo vệ lịch sử Git:** Không sử dụng `git push --force` hoặc `git reset --hard` trên hai nhánh dùng chung là `main` và `develop`.
3. **Tham khảo thêm:** Xem chi tiết tại [docs/git-workflow.md](./docs/git-workflow.md) và [docs/github-setup.md](./docs/github-setup.md).
