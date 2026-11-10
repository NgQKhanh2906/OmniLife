# GitHub Repository & Environment Setup - OmniLife Personal OS

## 1. Khởi Tạo Kho Lưu Trữ GitHub (Repository Initialization)

Thực hiện tuần tự các lệnh sau tại thư mục gốc `D:\Khanh\java\HackathonNCD\omnilife`:

```bash
git init
git branch -M main
git remote add origin https://github.com/<your-github-username>/omnilife.git
git checkout -b develop
```

### Cấu hình Branch Protection trên GitHub (Settings -> Branches)
- **Target branch:** `main`
- Bật **Require a pull request before merging**.
- Bật **Require status checks to pass before merging** (Chọn 2 jobs từ GitHub Actions: `backend-build-and-test` và `frontend-lint-and-typecheck`).
- Bật **Do not allow bypassing the above settings**.

---

## 2. Cấu Hình File `.gitignore` Chuẩn Cho Monorepo

Nội dung đầy đủ của file `.gitignore` đặt tại thư mục gốc `omnilife/.gitignore`:

```gitignore
# ==========================================
# 1. ENVIRONMENT & SECRETS (CRITICAL)
# ==========================================
.env
.env.local
.env.development.local
.env.test.local
.env.production.local
*.pem
*.key
application-secret.yml

# ==========================================
# 2. BACKEND (JAVA 21 / SPRING BOOT / MAVEN)
# ==========================================
backend/target/
backend/.mvn/timing.properties
backend/.mvn/wrapper/maven-wrapper.jar
*.class
*.log
*.jar
*.war
*.nar
*.ear
*.zip
*.tar.gz
*.rar
hs_err_pid*
replay_pid*

# ==========================================
# 3. FRONTEND (NEXT.JS 14 / NODE.JS)
# ==========================================
frontend/node_modules/
frontend/.next/
frontend/out/
frontend/build/
frontend/coverage/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.pnpm-debug.log*
*.tsbuildinfo
next-env.d.ts

# ==========================================
# 4. MYSQL WORKBENCH & LOCAL DATA
# ==========================================
database/*.mwb.bak
database/*.mwb.beforefix
mysql-data/
uploads/

# ==========================================
# 5. IDEs & OS FILES
# ==========================================
.idea/
*.iws
*.iml
*.ipr
.vscode/
.DS_Store
Thumbs.db
```

---

## 3. Danh Sách Đầy Đủ Các Biến Môi Trường (`.env.example`)

### 3.1. File `backend/.env.example` (Spring Boot 3.3)

```properties
# ===================================================================
# SERVER & APPLICATION PROFILE
# ===================================================================
SERVER_PORT=8080
SPRING_PROFILES_ACTIVE=local
APP_FRONTEND_URL=http://localhost:3000
APP_CORS_ALLOWED_ORIGINS=http://localhost:3000

# ===================================================================
# MYSQL 8.0 DATABASE CONFIGURATION (MYSQL WORKBENCH)
# ===================================================================
DB_HOST=localhost
DB_PORT=3306
DB_NAME=omnilife_db
DB_USERNAME=root
DB_PASSWORD=your_local_mysql_password

# ===================================================================
# JWT STATELESS SECURITY CONFIGURATION
# ===================================================================
# Chuỗi khóa bí mật HMAC-SHA256 dài tối thiểu 64 ký tự (256-bit+)
JWT_SECRET_KEY=7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a
JWT_ACCESS_TOKEN_EXPIRATION_MS=900000
JWT_REFRESH_TOKEN_EXPIRATION_MS=604800000
JWT_REFRESH_COOKIE_NAME=omnilife_rt
JWT_REFRESH_COOKIE_SECURE=false

# ===================================================================
# GOOGLE OAUTH2 & CALENDAR API (GOOGLE CLOUD CONSOLE - FREE TIER)
# ===================================================================
GOOGLE_CLIENT_ID=your_google_client_id.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_REDIRECT_URI=http://localhost:8080/login/oauth2/code/google

# ===================================================================
# EMAIL REMINDER NOTIFICATION (GMAIL SMTP - APP PASSWORD)
# ===================================================================
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=your_email@gmail.com
MAIL_APP_PASSWORD=your_16_char_gmail_app_password
MAIL_FROM_ADDRESS=noreply@omnilife.local

# ===================================================================
# FILE UPLOAD & SYLLABUS EXTRACTION LIMITS
# ===================================================================
MAX_FILE_UPLOAD_SIZE=15MB
STUDY_MATERIAL_STORAGE_DIR=./uploads/study-materials
```

### 3.2. File `frontend/.env.example` (Next.js 14)

```properties
# ===================================================================
# BACKEND REST API & OAUTH2 ENDPOINTS
# ===================================================================
NEXT_PUBLIC_API_BASE_URL=http://localhost:8080/api/v1
NEXT_PUBLIC_OAUTH2_GOOGLE_AUTH_URL=http://localhost:8080/oauth2/authorization/google

# ===================================================================
# APPLICATION & DOCUMENT PIP HUD SETTINGS
# ===================================================================
NEXT_PUBLIC_APP_NAME=OmniLife OS
NEXT_PUBLIC_DEFAULT_TIMEZONE=Asia/Ho_Chi_Minh
NEXT_PUBLIC_HUD_DEFAULT_WIDTH=340
NEXT_PUBLIC_HUD_DEFAULT_HEIGHT=460
```

---

## 4. Cấu Hình GitHub Actions CI/CD (`.github/workflows/ci-omnilife.yml`)

Pipeline này tự động khởi tạo một container **MySQL 8.0 thật**, chạy kiểm증 toàn bộ kịch bản Flyway Migration (`V1__`, `V2__`), chạy toàn bộ Unit/Integration Tests của Spring Boot và kiểm tra kiểu dữ liệu TypeScript của Next.js mỗi khi có lệnh `git push` hoặc `Pull Request`:

```yaml
name: OmniLife CI Pipeline

on:
  push:
    branches: [ "main", "develop" ]
  pull_request:
    branches: [ "main", "develop" ]

jobs:
  backend-build-and-test:
    name: Spring Boot 3 & MySQL 8 Verification
    runs-on: ubuntu-latest

    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: test_root_password
          MYSQL_DATABASE: omnilife_test_db
        ports:
          - 3306:3306
        options: >-
          --health-cmd="mysqladmin ping -h localhost -u root -ptest_root_password"
          --health-interval=10s
          --health-timeout=5s
          --health-retries=5

    defaults:
      run:
        working-directory: ./backend

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Set up JDK 21 LTS
        uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'
          cache: maven

      - name: Grant execute permission for mvnw
        run: chmod +x mvnw

      - name: Run Flyway Migration & JUnit 5 Tests
        env:
          DB_HOST: 127.0.0.1
          DB_PORT: 3306
          DB_NAME: omnilife_test_db
          DB_USERNAME: root
          DB_PASSWORD: test_root_password
          JWT_SECRET_KEY: 0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef
          GOOGLE_CLIENT_ID: mock_client_id
          GOOGLE_CLIENT_SECRET: mock_client_secret
        run: ./mvnw clean verify --no-transfer-progress

  frontend-lint-and-typecheck:
    name: Next.js 14 Lint & Type Check
    runs-on: ubuntu-latest

    defaults:
      run:
        working-directory: ./frontend

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Set up Node.js 20 LTS
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
          cache-dependency-path: frontend/package-lock.json

      - name: Install Dependencies
        run: npm ci

      - name: Run ESLint & TypeScript Compiler Check
        env:
          NEXT_PUBLIC_API_BASE_URL: http://localhost:8080/api/v1
          NEXT_PUBLIC_OAUTH2_GOOGLE_AUTH_URL: http://localhost:8080/oauth2/authorization/google
        run: |
          npm run lint
          npx tsc --noEmit
          npm run build
```

---

## 5. Cấu Hình Issue Templates & Pull Request Template

### 5.1. Feature / Task Template (`.github/ISSUE_TEMPLATE/task_implementation.md`)
```markdown
---
name: Task Implementation
about: Theo dõi việc thực thi một Task kỹ thuật từ roadmap.md
title: "[TASK-XX] Tên Task"
labels: ["enhancement"]
---

## 1. Mã Task & Mục tiêu
- **Mã Task:** `TASK-XX`
- **Phân hệ:** `productivity` | `study` | `finance` | `habit` | `journal` | `hud`

## 2. Danh sách File cần tạo / sửa
- [ ] File 1
- [ ] File 2

## 3. Tiêu chí nghiệm thu (Acceptance Criteria)
- [ ] Đã vượt qua toàn bộ JUnit 5 test cases / UI verification.
- [ ] Đã kiểm tra không phát sinh lỗi N+1 Query trong Hibernate log.
- [ ] Đã cập nhật trạng thái trong `docs/worklog.md`.
```

### 5.2. Bug Report Template (`.github/ISSUE_TEMPLATE/bug_report.md`)
```markdown
---
name: Bug Report
about: Báo cáo lỗi nghiệp vụ, lỗi truy vấn SQL hoặc lỗi giao diện
title: "[BUG] Mô tả ngắn gọn lỗi"
labels: ["bug"]
---

## 1. Các bước tái hiện lỗi (Steps to Reproduce)
1. Truy cập màn hình / Gọi API...
2. Truyền payload...
3. Quan sát lỗi trả về...

## 2. Kết quả mong đợi vs Kết quả thực tế
- **Mong đợi:**
- **Thực tế (Kèm Stack Trace / SQL Log):**

## 3. Phân tích Nguyên nhân gốc (Root Cause Analysis)
- Nguyên nhân nghi ngờ (Transaction, Locking, LazyInitializationException, CORS...):
```
