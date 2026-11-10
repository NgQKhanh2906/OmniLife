# Design System & UI/UX Specification - OmniLife Personal OS

## 1. Triết Lý Thiết Kế (Design Philosophy)

OmniLife là một công cụ được người dùng mở hàng giờ mỗi ngày, do đó hệ thống giao diện tuân thủ 4 nguyên tắc cốt lõi:
1. **High-Density Bento Grid:** Sắp xếp thông tin theo các khối thẻ (Bento Cards) có viền mảnh `1px`, mật độ thông tin cao nhưng thoáng mắt, không lãng phí diện tích màn hình.
2. **Dark-First Minimalist Aesthetics:** Lấy cảm hứng từ ngôn ngữ thiết kế của Linear, Raycast và Cron Calendar. Độ tương phản được kiểm soát chặt chẽ để không gây mỏi mắt khi học tập và làm việc ban đêm.
3. **Visual Urgency Hierarchy:** Màu sắc rực rỡ chỉ xuất hiện khi có thông tin cần hành động ngay (Task sắp trễ hạn, Ngân sách vượt ngưỡng, Môn học cần ôn bài cũ hôm nay). Các thành phần còn lại giữ tông màu trung tính.
4. **Keyboard-First & Zero-Friction:** Mọi thao tác chính đều có thể thực hiện qua hộp thoại điều khiển nhanh Command Palette (`Ctrl + K`) mà không cần rời tay khỏi bàn phím.

---

## 2. Bảng Màu Quy Chuẩn (Hex Color Palette)

### 2.1. Hệ Màu Nền & Bề Mặt (Surfaces & Backgrounds)

| Token Name | Dark Mode (Mặc định) | Light Mode | Mục đích sử dụng |
| :--- | :--- | :--- | :--- |
| `--bg-canvas` | `#090A0F` | `#F8FAFC` | Nền tổng thể của trang web (App Canvas) |
| `--bg-surface-1` | `#11131A` | `#FFFFFF` | Nền của các khối Bento Card, Sidebar, Calendar |
| `--bg-surface-2` | `#181B24` | `#F1F5F9` | Nền của thẻ con (Task Card, Input Field, Sub-panel) |
| `--bg-surface-hover` | `#1F2330` | `#E2E8F0` | Trạng thái Hover của các dòng dữ liệu và nút phụ |
| `--bg-glass` | `rgba(17, 19, 26, 0.82)` | `rgba(255, 255, 255, 0.85)` | Nền mờ (`backdrop-filter: blur(12px)`) cho Header, Modal và Audio Bar |
| `--border-subtle` | `#222634` | `#E2E8F0` | Viền mảnh `1px` phân tách các khối Bento và bảng |
| `--border-focus` | `#6366F1` | `#4F46E5` | Viền khi Focus vào ô nhập liệu hoặc thẻ đang chọn |

### 2.2. Hệ Màu Chữ (Typography Colors)

| Token Name | Dark Mode | Light Mode | Mục đích sử dụng |
| :--- | :--- | :--- | :--- |
| `--text-primary` | `#F3F4F6` | `#0F172A` | Tiêu đề chính, nội dung công việc, số tiền |
| `--text-secondary` | `#9CA3AF` | `#475569` | Mô tả phụ, tên danh mục, nhãn thời gian |
| `--text-muted` | `#6B7280` | `#94A3B8` | Placeholder, công việc đã hoàn thành (gạch ngang) |

### 2.3. Hệ Màu Thương Hiệu & Ngữ Nghĩa (Brand & Semantic Colors)

| Token Name | Mã Hex | Mục đích sử dụng |
| :--- | :--- | :--- |
| `--accent-primary` | `#6366F1` (Indigo 500) | Nút hành động chính (Primary CTA), trạng thái Active, tiến trình học tập |
| `--accent-hover` | `#4F46E5` (Indigo 600) | Trạng thái Hover của nút chính |
| `--semantic-success` | `#10B981` (Emerald 500) | Hoàn thành Task, Thu nhập (`INCOME`), Chuỗi thói quen đạt mục tiêu |
| `--semantic-warning` | `#F59E0B` (Amber 500) | Cảnh báo ngân sách chạm 80%, Bài học đến hạn ôn tập |
| `--semantic-danger` | `#EF4444` (Rose 500) | Công việc quá hạn, Chi tiêu vượt 100% ngân sách, Xóa dữ liệu |
| `--semantic-info` | `#3B82F6` (Blue 500) | Sự kiện đồng bộ từ Google Calendar, Thông báo lịch học |

### 2.4. Hệ Màu Phân Cấp Độ Khẩn Cấp Động (Dynamic Urgency Tier Colors)

| Mức độ khẩn cấp | Điểm số ($U$) | Màu Chữ / Nhãn | Màu Nền Badge | Màu Viền Trái Thẻ (`border-left: 3px`) |
| :--- | :--- | :--- | :--- | :--- |
| `OVERDUE` (Quá hạn) | $h \le 0$ | `#FCA5A5` | `rgba(239, 68, 68, 0.20)` | `#EF4444` (Kèm hiệu ứng xung nhịp nhẹ) |
| `EXTREME` (Khẩn cấp) | $80 \le U \le 100$ | `#FDBA74` | `rgba(249, 115, 22, 0.18)` | `#F97316` |
| `HIGH` (Ưu tiên cao) | $60 \le U < 80$ | `#FDE047` | `rgba(234, 179, 8, 0.15)` | `#EAB308` |
| `MODERATE` (Vừa phải) | $35 \le U < 60$ | `#93C5FD` | `rgba(59, 130, 246, 0.15)` | `#3B82F6` |
| `LOW` (Thong thả) | $0 \le U < 35$ | `#9CA3AF` | `rgba(156, 163, 175, 0.12)` | `#4B5563` |

---

## 3. Quy Chuẩn Kiểu Chữ (Typography System)

- **Font chữ giao diện chính (`--font-sans`):** `'Inter'`, `'Plus Jakarta Sans'`, `-apple-system`, `sans-serif`. Hỗ trợ đầy đủ dấu tiếng Việt sắc nét.
- **Font chữ số liệu & đếm ngược (`--font-mono`):** `'JetBrains Mono'`, `monospace`. Bắt buộc bật thuộc tính CSS `font-variant-numeric: tabular-nums` cho toàn bộ đồng hồ Pomodoro, điểm khẩn cấp, thời gian đếm ngược và số tiền tài chính để các chữ số không bị nhảy lệch chiều ngang khi đếm.

| Cấp độ chữ | Kích thước (rem / px) | Độ đậm (`font-weight`) | Chiều cao dòng (`line-height`) | Ứng dụng |
| :--- | :--- | :--- | :--- | :--- |
| `Display Timer` | `2.25rem` (`36px`) | `700` (Bold) | `1.1` | Đồng hồ Pomodoro trung tâm & trên Mini HUD |
| `Heading 1 (H1)` | `1.5rem` (`24px`) | `700` (Bold) | `1.25` | Tiêu đề trang (Dashboard, Study Workspace, Finance) |
| `Heading 2 (H2)` | `1.125rem` (`18px`) | `600` (SemiBold) | `1.35` | Tiêu đề khối Bento Card, Tiêu đề Modal/Drawer |
| `Heading 3 (H3)` | `0.9375rem` (`15px`) | `600` (SemiBold) | `1.4` | Tiêu đề Task, Tên môn học, Tên danh mục chi tiêu |
| `Body Default` | `0.875rem` (`14px`) | `400` (Regular) | `1.5` | Nội dung mô tả, trang Nhật ký, bảng dữ liệu |
| `Caption / Badge` | `0.75rem` (`12px`) | `500` (Medium) | `1.25` | Nhãn Urgency Score, Tags, Thời gian còn lại, Chú thích biểu đồ |

---

## 4. Hệ Thống Khoảng Cách, Khung Lưới & Bo Góc (Layout & Spacing)

1. **Đơn vị cơ sở (Base Spacing Unit):** Hệ bội số của `4px` (`4px`, `8px`, `12px`, `16px`, `20px`, `24px`, `32px`).
2. **Bố cục Khung chính (Application Shell Layout):**
   - **Left Collapsible Sidebar:** Rộng `240px` khi mở (thu gọn còn `64px`), cố định bên trái, chứa điều hướng 6 không gian làm việc chính (Dashboard, Calendar & Tasks, Study Workspace, Finance Ledger, Habits & Fitness, Daily Journal) và nút mở nhanh Mini HUD.
   - **Top Command Header:** Cao `56px`, chứa thanh tìm kiếm/lệnh nhanh (`Ctrl + K`), trạng thái đồng bộ Google Calendar, nút kích hoạt "Detach Mini HUD" và Avatar người dùng.
   - **Main Content Area:** Khung lưới Bento Grid 12 cột (`gap: 16px`, `padding: 20px`), tự động co giãn.
   - **Bottom Ambient Audio Bar:** Cao `52px`, cố định dưới đáy màn hình, cho phép bật/tắt nhạc Lo-Fi và trộn âm thanh trắng ở bất kỳ trang nào mà không bị ngắt nhạc khi chuyển route.
3. **Độ bo góc (Border Radius):**
   - Khối Bento Card lớn & Modal: `12px` (`rounded-xl`)
   - Thẻ Task, Ô nhập liệu, Nút bấm: `8px` (`rounded-lg`)
   - Nhãn Badge, Tag: `6px` (`rounded-md`)

---

## 5. Đặc Tả Các Component Cốt Lõi (Core UI Components)

### 5.1. Cửa Sổ Nổi Always-on-Top Mini HUD (Document PiP Window)
- **Kích thước mặc định:** Rộng `340px`, Cao `460px` (cho phép người dùng kéo giãn từ `280px` đến `420px`).
- **Bố cục 3 phần từ trên xuống dưới:**
  1. **Khu vực Header & Đồng hồ Tập trung (`110px`):** Hiển thị đồng hồ đếm ngược Pomodoro chữ lớn (`tabular-nums`), tên công việc đang tập trung, nút Play/Pause/Reset.
  2. **Danh sách Top 3 Công việc Khẩn cấp nhất (`280px`):**
     - Mỗi dòng hiển thị: Checkbox hoàn thành, Tiêu đề Task (tối đa 2 dòng), Nhãn điểm khẩn cấp (ví dụ: `Score: 89.4`) và đồng hồ đếm ngược tới hạn chót (`Còn 3h 12p`).
     - Có nút mở rộng nhanh để tick các `subtasks` con ngay trong cửa sổ nổi.
  3. **Thanh điều khiển Nhạc nền Mini (`50px`):** Nút Play/Pause nhạc Lo-Fi, thanh trượt âm lượng tổng và nút "Quay lại Web chính".

### 5.2. Thẻ Công Việc Động (Dynamic Urgency Task Card)
- Hiển thị viền trái dày `3px` mang màu sắc của `Urgency Tier`.
- Góc phải trên hiển thị huy hiệu điểm số khẩn cấp (`Urgency Badge`) có tooltip giải thích chi tiết cách tính điểm: `Điểm gốc (45) + Áp lực hạn chót (32.4) + Khối lượng (8.2) = 85.6`.
- Khi bấm vào thẻ, mở **Task Detail Slide-over Drawer** rộng `480px` trượt từ cạnh phải màn hình ra để xem và chỉnh sửa chi tiết mà không làm mất ngữ cảnh lịch tuần bên trái.

### 5.3. Bảng Ôn Tập Lặp Lại Ngắt Quãng (Spaced Repetition Review Deck)
- Hiển thị danh sách các chủ đề môn học (`study_topics`) có `next_review_date <= Hôm nay`.
- Khi người dùng bấm "Ôn tập", hiển thị ghi chú tóm tắt/tài liệu của chủ đề đó kèm 4 nút tự đánh giá trực quan:
  - `Quên (Học lại - 1 ngày)` - Màu Đỏ
  - `Khó nhớ (2 ngày)` - Màu Vàng
  - `Nhớ tốt (4 ngày)` - Màu Xanh Lam
  - `Rất thuộc (9 ngày)` - Màu Xanh Ngọc

### 5.4. Ma Trận Nhiệt Thói Quen (Habit Contribution Heatmap)
- Bảng lưới gồm 52 cột (tuần) x 7 hàng (thứ Hai đến Chủ Nhật), mỗi ô vuông kích thước `11px x 11px`, bo góc `2px`, khoảng cách `3px`.
- Độ đậm của màu xanh ngọc (`#064E3B` -> `#047857` -> `#10B981` -> `#34D399`) thể hiện tỷ lệ hoàn thành các thói quen và bài tập thể thao trong ngày đó.

---

## 6. Quy Chuẩn Trạng Thái Màn Hình (Screen States)

1. **Trạng thái Đang tải (Loading State):**
   - Tuyệt đối không dùng vòng xoay (Spinner) chắn toàn màn hình khi chuyển tab.
   - Sử dụng khung xương **Shimmer Skeleton** khớp đúng kích thước của Bento Card, Task Row và Calendar Grid để tránh hiện tượng giật bố cục (Cumulative Layout Shift - CLS = 0).
2. **Trạng thái Trống (Empty State):**
   - Mỗi phân hệ khi chưa có dữ liệu đều hiển thị hình minh họa SVG nét mảnh tối giản kèm câu hướng dẫn và nút hành động trực tiếp:
     - *Task List trống:* "Bạn đã hoàn thành toàn bộ công việc khẩn cấp. Nhấn `C` để thêm công việc mới hoặc nghỉ ngơi."
     - *Study Review trống:* "Không có bài cũ nào đến hạn ôn tập trong hôm nay. Bạn đang bám sát lộ trình thi."
3. **Trạng thái Lỗi & Xung đột (Error & Conflict State):**
   - Lỗi biểu mẫu (Validation Error) hiển thị ngay bên dưới trường nhập liệu với chữ màu `#EF4444` cỡ `12px`.
   - Khi mất kết nối tới Backend hoặc xảy ra lỗi `409 Conflict` (do sửa đồng thời), hiển thị thanh thông báo (Toast Notification) ở góc dưới bên phải kèm nút **"Tải lại dữ liệu mới nhất"**.
