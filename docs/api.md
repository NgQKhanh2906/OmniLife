# RESTful API Specification - OmniLife Personal OS

## 1. Quy Chuẩn Giao Tiếp Chung (API Conventions)

- **Base URL:** `http://localhost:8080/api/v1` (Local) | `https://api.omnilife.app/api/v1` (Production)
- **Định dạng dữ liệu:** `application/json; charset=utf-8` (Ngoại trừ API tải lên tài liệu học tập sử dụng `multipart/form-data`).
- **Định dạng ngày giờ:** Chuẩn `ISO-8601` (`YYYY-MM-DDTHH:mm:ssZ` cho `DATETIME` và `YYYY-MM-DD` cho `DATE`).
- **Xác thực (Authentication Middleware):**
  - Toàn bộ các API (trừ `/api/v1/auth/register`, `/api/v1/auth/login`, `/api/v1/auth/refresh`, `/oauth2/**`) bắt buộc đính kèm Header: `Authorization: Bearer <accessToken>`.
  - `user_id` luôn được trích xuất từ `SecurityContextHolder` tại tầng Controller thông qua `@CurrentUser CustomUserDetails currentUser`.

### 1.1. Cấu Trúc JSON Phản Hồi Chuẩn (`ApiResponse<T>`)

```json
{
  "success": true,
  "timestamp": "2026-11-10T01:30:00Z",
  "message": "Thao tác thành công",
  "data": {}
}
```

### 1.2. Cấu Trúc Phân Trang Chuẩn (`PageResponse<T>`)

```json
{
  "success": true,
  "timestamp": "2026-11-10T01:30:00Z",
  "message": "Lấy danh sách thành công",
  "data": {
    "items": [],
    "page": 0,
    "size": 20,
    "totalElements": 45,
    "totalPages": 3,
    "hasNext": true
  }
}
```

### 1.3. Cấu Trúc Phản Hồi Lỗi Chuẩn (`ApiErrorResponse`)

```json
{
  "success": false,
  "timestamp": "2026-11-10T01:30:00Z",
  "errorCode": "TASK_VERSION_CONFLICT",
  "message": "Công việc đã được cập nhật ở một cửa sổ khác. Vui lòng đồng bộ lại dữ liệu.",
  "path": "/api/v1/tasks/101/status",
  "fieldErrors": [
    {
      "field": "version",
      "rejectedValue": 2,
      "reason": "Current database version is 3"
    }
  ]
}
```

---

## 2. Phân Hệ 1: Xác Thực & Hồ Sơ Cá Nhân (Auth & User Profile APIs)

### 2.1. Đăng ký tài khoản mới
- **Endpoint:** `POST /api/v1/auth/register`
- **Auth Required:** Không
- **Request Payload:**
```json
{
  "fullName": "Nguyễn Quốc Khánh",
  "email": "khanh.student@omnilife.local",
  "password": "OmniLife@2026",
  "timezone": "Asia/Ho_Chi_Minh"
}
```
- **Response (`201 Created`):**
```json
{
  "success": true,
  "timestamp": "2026-11-10T01:30:00Z",
  "message": "Đăng ký tài khoản thành công",
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresInSeconds": 900,
    "user": {
      "id": 1,
      "email": "khanh.student@omnilife.local",
      "fullName": "Nguyễn Quốc Khánh",
      "avatarUrl": null,
      "authProvider": "LOCAL"
    }
  }
}
```

### 2.2. Đăng nhập bằng Email & Mật khẩu
- **Endpoint:** `POST /api/v1/auth/login`
- **Auth Required:** Không
- **Request Payload:**
```json
{
  "email": "khanh.student@omnilife.local",
  "password": "OmniLife@2026"
}
```
- **Response (`200 OK`):** Trả về `AuthResponse` giống mục 2.1 kèm `Set-Cookie: omnilife_rt=<refresh_token>; HttpOnly; Path=/api/v1/auth; Max-Age=604800; SameSite=Lax`.

### 2.3. Làm mới Access Token (Refresh Token Rotation)
- **Endpoint:** `POST /api/v1/auth/refresh`
- **Auth Required:** Nhận `omnilife_rt` từ HttpOnly Cookie hoặc trường `refreshToken` trong Body.
- **Request Payload:**
```json
{
  "refreshToken": "def50200a1b2c3d4e5f6..."
}
```
- **Response (`200 OK`):**
```json
{
  "success": true,
  "timestamp": "2026-11-10T01:45:00Z",
  "message": "Làm mới token thành công",
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.newPayload...",
    "expiresInSeconds": 900
  }
}
```

### 2.4. Đăng xuất & Thu hồi Token
- **Endpoint:** `POST /api/v1/auth/logout`
- **Auth Required:** `Bearer <accessToken>`
- **Response (`200 OK`):** Đánh dấu `is_revoked = 1` trong bảng `refresh_tokens` và xóa Cookie.

### 2.5. Lấy & Cập nhật cấu hình cá nhân hóa
- **Endpoints:**
  - `GET /api/v1/users/me/preferences`
  - `PUT /api/v1/users/me/preferences`
- **Request/Response Payload (`200 OK`):**
```json
{
  "themeMode": "DARK",
  "timezone": "Asia/Ho_Chi_Minh",
  "dailyFocusGoalMins": 240,
  "pomodoroWorkMins": 25,
  "pomodoroShortBreakMins": 5,
  "pomodoroLongBreakMins": 15,
  "urgencyWeights": {
    "basePriorityMultiplier": 1.0,
    "deadlineDecayMultiplier": 1.0,
    "effortRatioMultiplier": 1.0,
    "scheduledTodayBonus": 10.0
  }
}
```

---

## 3. Phân Hệ 2: Công Việc Ưu Tiên Động, Lịch Biểu & Mini HUD (Productivity APIs)

### 3.1. Lấy danh sách Công việc đã xếp hạng theo Điểm Khẩn Cấp Động
- **Endpoint:** `GET /api/v1/tasks`
- **Query Params:**
  - `status` (optional): `TODO`, `IN_PROGRESS`, `DONE`, `ALL` (Mặc định: `TODO,IN_PROGRESS`)
  - `courseId` (optional): Lọc bài tập theo môn học
  - `scheduledDate` (optional): `YYYY-MM-DD` (Lọc các việc cần làm trong ngày cụ thể khi bấm vào ngày đó)
  - `sortBy` (optional): `URGENCY_DESC` (Mặc định), `DUE_DATE_ASC`, `CREATED_DESC`
  - `page` (default `0`), `size` (default `20`)
- **Response (`200 OK`):**
```json
{
  "success": true,
  "timestamp": "2026-11-10T02:00:00Z",
  "message": "Lấy danh sách công việc thành công",
  "data": {
    "items": [
      {
        "id": 101,
        "courseId": 1,
        "courseCode": "IT4090",
        "title": "Nộp bài tập lớn Thiết kế CSDL MySQL Workbench",
        "status": "TODO",
        "basePriority": "CRITICAL",
        "estimatedMinutes": 120,
        "actualFocusedMinutes": 50,
        "dueAt": "2026-11-10T05:00:00Z",
        "scheduledStartAt": "2026-11-10T02:00:00Z",
        "scheduledEndAt": "2026-11-10T04:00:00Z",
        "urgencyScore": 98.40,
        "urgencyTier": "EXTREME",
        "urgencyBreakdown": {
          "baseScore": 65.0,
          "deadlineDecayScore": 23.4,
          "effortPressureScore": 10.0,
          "scheduledTodayBonus": 10.0
        },
        "tags": ["database", "assignment"],
        "isPinnedHud": true,
        "subtaskProgress": {
          "completed": 1,
          "total": 3
        },
        "version": 2
      }
    ],
    "page": 0,
    "size": 20,
    "totalElements": 1,
    "totalPages": 1,
    "hasNext": false
  }
}
```

### 3.2. Xem chi tiết một Công việc (Khi bấm vào Task hoặc Ngày trên Lịch)
- **Endpoint:** `GET /api/v1/tasks/{taskId}`
- **Response (`200 OK`):** Trả về toàn bộ thông tin của Task kèm mảng `subtasks` và mảng `recentFocusSessions`:
```json
{
  "success": true,
  "timestamp": "2026-11-10T02:00:00Z",
  "message": "Lấy chi tiết công việc thành công",
  "data": {
    "id": 101,
    "courseId": 1,
    "title": "Nộp bài tập lớn Thiết kế CSDL MySQL Workbench",
    "description": "Hoàn thiện sơ đồ ERD 24 bảng và kiểm tra lệnh EXPLAIN.",
    "status": "TODO",
    "basePriority": "CRITICAL",
    "estimatedMinutes": 120,
    "actualFocusedMinutes": 50,
    "dueAt": "2026-11-10T05:00:00Z",
    "scheduledStartAt": "2026-11-10T02:00:00Z",
    "scheduledEndAt": "2026-11-10T04:00:00Z",
    "urgencyScore": 98.40,
    "urgencyTier": "EXTREME",
    "tags": ["database", "assignment"],
    "isPinnedHud": true,
    "version": 2,
    "subtasks": [
      { "id": 501, "title": "Chuẩn hóa bảng lên 3NF", "isCompleted": true, "sortOrder": 1 },
      { "id": 502, "title": "Đánh Composite Index", "isCompleted": false, "sortOrder": 2 },
      { "id": 503, "title": "Xuất file báo cáo PDF", "isCompleted": false, "sortOrder": 3 }
    ],
    "focusSessions": [
      {
        "id": 801,
        "plannedMinutes": 25,
        "completedMinutes": 25,
        "status": "COMPLETED",
        "startedAt": "2026-11-09T14:00:00Z",
        "endedAt": "2026-11-09T14:25:00Z"
      }
    ]
  }
}
```

### 3.3. Tạo mới Công việc (Tự động kích hoạt tính điểm Urgency Score)
- **Endpoint:** `POST /api/v1/tasks`
- **Request Payload:**
```json
{
  "courseId": 1,
  "title": "Hoàn thiện bộ 14 file Spec chuẩn cho dự án OmniLife",
  "description": "Kiểm tra đồng bộ giữa Database schema và REST API payload.",
  "basePriority": "HIGH",
  "estimatedMinutes": 90,
  "dueAt": "2026-11-11T16:00:00Z",
  "scheduledStartAt": "2026-11-10T08:00:00Z",
  "scheduledEndAt": "2026-11-10T09:30:00Z",
  "tags": ["architecture", "spring-boot"],
  "isPinnedHud": false,
  "subtasks": [
    { "title": "Viết file architecture.md", "sortOrder": 1 },
    { "title": "Viết file database.md", "sortOrder": 2 },
    { "title": "Viết file api.md", "sortOrder": 3 }
  ]
}
```
- **Response (`201 Created`):** Trả về đối tượng `TaskDetailResponse` vừa tạo kèm `urgencyScore` và `urgencyTier` đã tính toán.

### 3.4. Cập nhật Công việc & Kiểm soát Optimistic Locking
- **Endpoint:** `PUT /api/v1/tasks/{taskId}`
- **Request Payload:** Bắt buộc gửi kèm trường `version` hiện tại của bản ghi:
```json
{
  "title": "Hoàn thiện bộ 14 file Spec chuẩn cho dự án OmniLife",
  "description": "Đã cập nhật thêm ERD 24 bảng.",
  "status": "IN_PROGRESS",
  "basePriority": "CRITICAL",
  "estimatedMinutes": 90,
  "dueAt": "2026-11-11T16:00:00Z",
  "scheduledStartAt": "2026-11-10T08:00:00Z",
  "scheduledEndAt": "2026-11-10T09:30:00Z",
  "tags": ["architecture", "spring-boot"],
  "isPinnedHud": true,
  "version": 2
}
```
- **Response:** `200 OK` (nếu `version` khớp, tự động tăng lên `version: 3`) hoặc `409 Conflict` (nếu `version` lệch).

### 3.5. Đổi nhanh Trạng thái Task hoặc Tick Subtask (Dùng cho Mini HUD & Lịch)
- **Endpoints:**
  - `PATCH /api/v1/tasks/{taskId}/status` -> Body: `{ "status": "DONE", "version": 2 }`
  - `PATCH /api/v1/tasks/{taskId}/subtasks/{subtaskId}/toggle` -> Body: `{ "isCompleted": true }`
  - `DELETE /api/v1/tasks/{taskId}` -> Thực hiện xóa mềm (`is_deleted = 1`).

### 3.6. Lấy Dữ Liệu Tối Ưu Cho Cửa Sổ Nổi Always-on-Top Mini HUD
- **Endpoint:** `GET /api/v1/hud/summary`
- **Mô tả:** Trả về Top 3 công việc khẩn cấp nhất (hoặc được ghim `is_pinned_hud = 1`), thống kê số phút đã tập trung hôm nay và buổi học sắp diễn ra gần nhất.
- **Response (`200 OK`):**
```json
{
  "success": true,
  "timestamp": "2026-11-10T02:00:00Z",
  "message": "Lấy dữ liệu Mini HUD thành công",
  "data": {
    "todayFocusedMinutes": 125,
    "dailyFocusGoalMinutes": 240,
    "upcomingClass": {
      "courseCode": "IT4090",
      "courseName": "Phát triển Ứng dụng Enterprise với Spring Boot",
      "startTime": "07:30:00",
      "room": "B1-302",
      "startsInMinutes": 45
    },
    "topUrgentTasks": [
      {
        "id": 101,
        "title": "Nộp bài tập lớn Thiết kế CSDL MySQL Workbench",
        "urgencyScore": 98.40,
        "urgencyTier": "EXTREME",
        "dueAt": "2026-11-10T05:00:00Z",
        "version": 2,
        "subtasks": [
          { "id": 502, "title": "Đánh Composite Index", "isCompleted": false },
          { "id": 503, "title": "Xuất file báo cáo PDF", "isCompleted": false }
        ]
      }
    ]
  }
}
```

### 3.7. Quản lý Lịch Biểu Tuần/Tháng & Phiên Tập Trung Pomodoro
- **`GET /api/v1/calendar/unified-view?startAt=2026-11-09T00:00:00Z&endAt=2026-11-15T23:59:59Z`**
  - Trả về gộp 3 luồng sự kiện trong tuần: (1) `calendarEvents` cá nhân & Google, (2) `timeBlockedTasks` (các task có `scheduled_start_at`), và (3) `classSchedules` (thời khóa biểu trên trường).
- **`POST /api/v1/calendar/events`** / **`PUT /api/v1/calendar/events/{id}`** / **`DELETE /api/v1/calendar/events/{id}`**: CRUD sự kiện lịch biểu.
- **`POST /api/v1/calendar/google-sync`**: Kích hoạt đồng bộ sự kiện từ Google Calendar API về bảng `calendar_events`.
- **`POST /api/v1/focus-sessions`**: Lưu kết quả phiên chạy đồng hồ Pomodoro và tự động cộng số phút vào `tasks.actual_focused_minutes`.

---

## 4. Phân Hệ 3: Không Gian Học Tập & Ôn Thi (Study Workspace APIs)

### 4.1. Quản lý Học kỳ, Môn học & Thời khóa biểu
- **`GET /api/v1/study/semesters`** & **`POST /api/v1/study/semesters`**: Lấy danh sách và tạo học kỳ mới.
- **`GET /api/v1/study/courses?semesterId=1`**: Lấy danh sách môn học kèm thống kê tiến độ nắm vững kiến thức (`masteryBreakdown`) và lịch học hàng tuần.
- **`POST /api/v1/study/courses`**:
```json
{
  "semesterId": 1,
  "courseCode": "IT4090",
  "name": "Phát triển Ứng dụng Enterprise với Spring Boot",
  "credits": 4,
  "instructorName": "TS. Trần Văn An",
  "roomLocation": "B1-302",
  "targetGrade": 9.0,
  "colorHex": "#6366F1",
  "classSchedules": [
    {
      "dayOfWeek": 1,
      "startTime": "07:30:00",
      "endTime": "10:00:00",
      "room": "B1-302",
      "remindBeforeMins": 30
    },
    {
      "dayOfWeek": 3,
      "startTime": "07:30:00",
      "endTime": "10:00:00",
      "room": "B1-302",
      "remindBeforeMins": 30
    }
  ]
}
```

### 4.2. Tải lên Tài liệu Học tập & Tự động Bóc tách Danh sách Chủ đề cần học
- **Endpoint:** `POST /api/v1/study/courses/{courseId}/materials/extract`
- **Content-Type:** `multipart/form-data` (`file`: Tệp PDF/DOCX/MD, `title`: Tên giáo trình, `autoCreateTopics`: `true`)
- **Response (`201 Created`):**
```json
{
  "success": true,
  "timestamp": "2026-11-10T03:00:00Z",
  "message": "Đã trích xuất 4 chủ đề học tập từ tài liệu",
  "data": {
    "materialId": 12,
    "title": "Giáo trình Spring Boot 3 Chuyên sâu",
    "fileType": "PDF",
    "extractedTopics": [
      {
        "id": 301,
        "chapterTitle": "Chương 1: IoC Container, ApplicationContext và Bean Lifecycle",
        "masteryLevel": "NEW",
        "plannedStudyDate": "2026-11-11",
        "isExamScope": true,
        "sortOrder": 1
      },
      {
        "id": 302,
        "chapterTitle": "Chương 2: Cơ chế hoạt động của Spring AOP Proxy và @Transactional",
        "masteryLevel": "NEW",
        "plannedStudyDate": "2026-11-12",
        "isExamScope": true,
        "sortOrder": 2
      }
    ]
  }
}
```

### 4.3. Lấy Danh sách Bài Cũ Cần Ôn Tập Hôm Nay (Spaced Repetition Queue)
- **Endpoint:** `GET /api/v1/study/reviews/due-today`
- **Response (`200 OK`):** Trả về toàn bộ các `study_topics` có `next_review_date <= CURDATE()` hoặc có `planned_study_date == CURDATE()`.

### 4.4. Nộp Kết Quả Ôn Tập Bài Cũ (Chạy Thuật Toán SM-2)
- **Endpoint:** `POST /api/v1/study/topics/{topicId}/review`
- **Request Payload:**
```json
{
  "qualityRating": 4,
  "summaryNotes": "Đã nắm chắc: @Transactional chỉ hoạt động khi gọi qua Spring Proxy từ bean bên ngoài, không hoạt động khi self-invocation."
}
```
- **Response (`200 OK`):**
```json
{
  "success": true,
  "timestamp": "2026-11-10T03:15:00Z",
  "message": "Đã cập nhật lịch ôn tập tiếp theo theo thuật toán SM-2",
  "data": {
    "topicId": 302,
    "chapterTitle": "Chương 2: Cơ chế hoạt động của Spring AOP Proxy và @Transactional",
    "previousMasteryLevel": "LEARNING",
    "newMasteryLevel": "REVIEWING",
    "repetitionCount": 2,
    "previousIntervalDays": 1,
    "newIntervalDays": 3,
    "newEasinessFactor": 2.50,
    "nextReviewDate": "2026-11-13"
  }
}
```

### 4.5. Đếm ngược Kỳ thi & Đánh giá Mức độ Sẵn sàng (Exam Readiness)
- **Endpoints:**
  - `POST /api/v1/study/exams`: Tạo lịch thi mới cho môn học.
  - `GET /api/v1/study/exams/countdown-readiness`:
- **Response (`200 OK`):**
```json
{
  "success": true,
  "timestamp": "2026-11-10T03:20:00Z",
  "message": "Lấy danh sách đếm ngược kỳ thi thành công",
  "data": [
    {
      "examId": 5,
      "courseId": 1,
      "courseCode": "IT4090",
      "courseName": "Phát triển Ứng dụng Enterprise với Spring Boot",
      "examTitle": "Thi Cuối Kỳ",
      "examType": "WRITTEN",
      "examAt": "2026-11-24T08:00:00Z",
      "daysRemaining": 14,
      "room": "Hội trường C2",
      "totalExamTopics": 12,
      "masteredOrReviewingTopics": 8,
      "newOrLearningTopics": 4,
      "readinessPercentage": 66.67,
      "recommendedTopicsPerDay": 0.3,
      "statusAlert": "ON_TRACK"
    }
  ]
}
```

---

## 5. Phân Hệ 4: Quản Lý Chi Tiêu Cá Nhân (Finance Ledger APIs)

### 5.1. Quản lý Ví & Danh mục
- **`GET /api/v1/finance/wallets`** & **`POST /api/v1/finance/wallets`**: Lấy danh sách ví kèm tổng tài sản ròng (`totalNetWorth`) và tạo ví mới.
- **`GET /api/v1/finance/categories`** & **`POST /api/v1/finance/categories`**: Lấy và tạo danh mục thu/chi.

### 5.2. Ghi nhận Giao dịch Thu / Chi / Chuyển ví (Sử dụng Pessimistic Lock)
- **Endpoint:** `POST /api/v1/finance/transactions`
- **Request Payload:**
```json
{
  "walletId": 1,
  "targetWalletId": null,
  "categoryId": 3,
  "type": "EXPENSE",
  "amount": 65000.00,
  "note": "Ăn trưa và cà phê ôn bài tại thư viện",
  "transactionDate": "2026-11-10T05:30:00Z"
}
```
- **Response (`201 Created`):**
```json
{
  "success": true,
  "timestamp": "2026-11-10T05:30:05Z",
  "message": "Đã ghi nhận giao dịch thành công",
  "data": {
    "transactionId": 905,
    "type": "EXPENSE",
    "amount": 65000.00,
    "walletId": 1,
    "walletName": "Ví Tiền mặt",
    "walletBalanceAfter": 1185000.00,
    "budgetAlert": {
      "categoryId": 3,
      "categoryName": "Ăn uống hàng ngày",
      "monthYear": "2026-11",
      "limitAmount": 3000000.00,
      "spentAmount": 2615000.00,
      "usagePercentage": 87.17,
      "alertLevel": "WARNING_THRESHOLD_EXCEEDED"
    }
  }
}
```

### 5.3. Quản lý Ngân sách Tháng & Báo cáo Thống kê Dòng tiền
- **`GET /api/v1/finance/budgets?monthYear=2026-11`** & **`POST /api/v1/finance/budgets`**: Thiết lập và theo dõi hạn mức chi tiêu từng danh mục trong tháng.
- **`GET /api/v1/finance/analytics/monthly-summary?monthYear=2026-11`**:
```json
{
  "success": true,
  "timestamp": "2026-11-10T05:35:00Z",
  "message": "Lấy báo cáo tài chính tháng 2026-11 thành công",
  "data": {
    "monthYear": "2026-11",
    "totalIncome": 5500000.00,
    "totalExpense": 3390000.00,
    "netSavings": 2110000.00,
    "savingsRatePercentage": 38.36,
    "expenseByCategory": [
      { "categoryId": 3, "categoryName": "Ăn uống hàng ngày", "amount": 2550000.00, "percentage": 75.22, "colorHex": "#F59E0B" },
      { "categoryId": 4, "categoryName": "Giải trí & Cà phê", "amount": 520000.00, "percentage": 15.34, "colorHex": "#EF4444" },
      { "categoryId": 5, "categoryName": "Sách vở & Tài liệu", "amount": 320000.00, "percentage": 9.44, "colorHex": "#10B981" }
    ]
  }
}
```

---

## 6. Phân Hệ 5: Thói Quen, Thể Thao, Nhật Ký & Âm Thanh (Habit, Journal & Audio APIs)

### 6.1. Điểm danh Thói quen & Thể thao hàng ngày
- **`GET /api/v1/habits?date=2026-11-10`**: Lấy danh sách các thói quen kèm trạng thái đã hoàn thành trong ngày và chuỗi `currentStreak`.
- **`POST /api/v1/habits/{habitId}/check-in`**:
```json
{
  "logDate": "2026-11-10",
  "achievedValue": 5.20,
  "note": "Chạy bộ 5.2km quanh công viên buổi chiều"
}
```
- **`GET /api/v1/habits/heatmap?year=2026`**: Trả về mảng 365 ngày `{ "date": "2026-11-10", "completedCount": 4, "totalActiveHabits": 4, "intensityLevel": 4 }` để vẽ biểu đồ nhiệt.

### 6.2. Nhật ký Hàng ngày, Chỉ số Tâm trạng & Bản chụp Hoạt động Tự động
- **Endpoint:** `GET /api/v1/journals/by-date?date=2026-11-10`
- **Mô tả:** Lấy nội dung nhật ký của ngày `2026-11-10` kèm bản chụp tự động (`dailySnapshot`) tổng hợp từ bảng `activity_logs`, `tasks`, `focus_sessions`, `transactions`.
- **Response (`200 OK`):**
```json
{
  "success": true,
  "timestamp": "2026-11-10T15:00:00Z",
  "message": "Lấy dữ liệu nhật ký ngày 2026-11-10 thành công",
  "data": {
    "id": 42,
    "entryDate": "2026-11-10",
    "title": "Ngày hoàn thành thiết kế CSDL & hiểu rõ Locking trong MySQL",
    "contentMarkdown": "Hôm nay đã hoàn thành 4 phiên Pomodoro liên tục và nắm chắc sự khác biệt giữa Optimistic Lock và Pessimistic Lock.",
    "moodScore": 5,
    "energyLevel": 4,
    "moodTags": ["Tập trung", "Hào hứng", "Năng suất"],
    "isBookmarked": true,
    "dailySnapshot": {
      "completedTasksCount": 3,
      "completedTaskTitles": [
        "Nộp bài tập lớn Thiết kế CSDL MySQL Workbench",
        "Fix lỗi N+1 Query trong module Study Workspace",
        "Giải 3 bài thuật toán Quy hoạch động"
      ],
      "totalFocusedMinutes": 125,
      "reviewedStudyTopicsCount": 2,
      "completedHabitsCount": 4,
      "totalSpentAmount": 65000.00
    }
  }
}
```
- **`PUT /api/v1/journals/by-date?date=2026-11-10`**: Tạo mới hoặc cập nhật (Upsert) trang nhật ký và chỉ số tâm trạng của ngày được chọn.

### 6.3. Quản lý Preset Âm thanh Tập trung (Lo-Fi & White Noise Mixer)
- **`GET /api/v1/audio-presets`** & **`POST /api/v1/audio-presets`** & **`DELETE /api/v1/audio-presets/{id}`**:
```json
{
  "name": "Code Đêm Tập Trung",
  "lofiTrackKey": "midnight-coding",
  "lofiVolume": 60,
  "ambientMix": {
    "soft-rain": 45,
    "ocean-waves": 0,
    "coffee-shop": 0,
    "campfire-crackle": 25,
    "mech-keyboard": 15
  },
  "isDefault": true
}
```

---

## 7. Bảng Mã Lỗi Chuẩn Toàn Hệ Thống (Standard Error Code Catalog)

| Mã lỗi (`errorCode`) | HTTP Status | Tình huống phát sinh |
| :--- | :--- | :--- |
| `AUTH_INVALID_CREDENTIALS` | `401 Unauthorized` | Sai email hoặc mật khẩu khi đăng nhập |
| `AUTH_TOKEN_EXPIRED` | `401 Unauthorized` | `accessToken` đã hết hạn 15 phút (Client cần gọi `/api/v1/auth/refresh`) |
| `AUTH_REFRESH_TOKEN_COMPROMISED` | `401 Unauthorized` | Phát hiện sử dụng lại Refresh Token cũ đã bị xoay vòng/thu hồi |
| `USER_EMAIL_ALREADY_EXISTS` | `409 Conflict` | Đăng ký bằng email đã tồn tại trong bảng `users` |
| `RESOURCE_NOT_FOUND` | `404 Not Found` | Không tìm thấy bản ghi hoặc bản ghi thuộc về `user_id` khác |
| `TASK_VERSION_CONFLICT` | `409 Conflict` | Lỗi `ObjectOptimisticLockingFailureException` khi sửa Task đồng thời ở 2 cửa sổ |
| `STUDY_DOCUMENT_UNREADABLE` | `422 Unprocessable Entity` | File PDF tải lên bị khóa mật khẩu hoặc không có lớp văn bản để trích xuất |
| `WALLET_INSUFFICIENT_BALANCE` | `400 Bad Request` | Số dư ví không đủ khi thực hiện chuyển tiền (`TRANSFER`) |
| `BUDGET_DUPLICATE_MONTH` | `409 Conflict` | Đã tồn tại hạn mức ngân sách cho danh mục này trong tháng `YYYY-MM` |
| `VALIDATION_FAILED` | `400 Bad Request` | Dữ liệu đầu vào vi phạm ràng buộc `@NotBlank`, `@Positive`, `@Min`, `@Max` |
