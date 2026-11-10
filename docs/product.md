# Product Specification - OmniLife Personal OS

## 1. Tầm Nhìn Sản Phẩm (Product Vision)

**OmniLife** được định vị là một **Hệ điều hành Năng suất và Đời sống Cá nhân (Personal Life Operating System)** dành cho sinh viên đại học, lập trình viên và người làm việc trí óc. Thay vì phải mở rời rạc 6 ứng dụng khác nhau (Google Calendar để xem lịch, Todoist để liệt kê công việc, Anki/Notion để theo dõi ôn thi, MoneyLover để ghi chép chi tiêu, Strava/Habitica để điểm danh tập luyện, DayOne để viết nhật ký), OmniLife gom toàn bộ vào một không gian thống nhất có tính liên kết dữ liệu chặt chẽ, giao diện tối giản tinh tế và khả năng ghim cửa sổ nhắc nhở nổi trực tiếp trên màn hình máy tính.

---

## 2. Chân Dung Người Dùng (User Personas)

### Persona 1: Sinh viên Công nghệ Thông tin / Kỹ thuật (Primary Persona)
- **Độ tuổi:** 18 - 23 tuổi.
- **Đặc điểm hành vi:**
  - Thường xuyên ngồi trước máy tính từ 6 đến 10 giờ mỗi ngày để học tập, làm đồ án và luyện lập trình.
  - Có lịch học trên trường cố định xen kẽ lịch họp nhóm, deadline bài tập lớn (Assignment) và lịch thi giữa kỳ/cuối kỳ dày đặc.
  - Quản lý ngân sách sinh hoạt hàng tháng giới hạn (từ gia đình gửi hoặc đi làm thêm).
  - Mong muốn duy trì thói quen tập thể thao, đọc tài liệu và ghi chép lại quá trình phát triển bản thân.
- **Điểm đau (Pain Points):**
  1. **Mù mờ về độ khẩn cấp:** Ghi hàng chục việc vào To-do list với nhãn ưu tiên P1, P2 tĩnh, dẫn đến những việc quan trọng nhưng hạn nộp còn vài ngày bị lãng quên cho tới khi sát giờ nộp.
  2. **Mất tập trung khi rời tab trình duyệt:** Khi mở IDE viết code hoặc đọc tài liệu PDF toàn màn hình, tab quản lý công việc bị che khuất khiến người dùng quên mất việc đang cần hoàn thành và sa đà vào việc khác.
  3. **Ôn thi bị động (Cramming):** Không biết cách phân bổ đề cương môn học theo ngày và không có cơ chế nhắc ôn lại kiến thức cũ đã học cách đó vài ngày.
  4. **Phân mảnh dữ liệu cá nhân:** Chi tiêu, lịch tập luyện, nhật ký và kết quả học tập nằm ở các ứng dụng riêng lẻ, không thể nhìn thấy bức tranh tổng quan của một ngày.

### Persona 2: Lập trình viên Tự do / Học viên Cao học (Secondary Persona)
- **Độ tuổi:** 22 - 30 tuổi.
- **Đặc điểm hành vi:**
  - Làm việc độc lập (Remote/Freelance), tự quản lý thời gian biểu và các đầu việc dự án.
  - Cần môi trường làm việc yên tĩnh với nhạc nền Lo-Fi / White Noise và đồng hồ đếm ngược Pomodoro.
- **Điểm đau (Pain Points):**
  - Thiếu một trung tâm điều khiển (Command Center) khởi động nhanh mỗi buổi sáng tích hợp cả lịch Google Calendar, các nhiệm vụ ưu tiên cao nhất, sổ thu chi và nhật ký công việc.

---

## 3. Bài Toán Giải Quyết & Giá Trị Cốt Lõi (Unique Selling Proposition - USP)

| Vấn đề của các công cụ hiện tại | Giải pháp Độc quyền của OmniLife (USP) |
| :--- | :--- |
| **Độ ưu tiên tĩnh (Static Priority):** Trong Todoist hoặc Google Tasks, một task gắn nhãn Priority 2 sẽ mãi nằm dưới Priority 1 dù task Priority 2 chỉ còn 2 tiếng nữa là hết hạn. | **Dynamic Urgency Scoring Engine:** Điểm khẩn cấp là một hàm số biến thiên liên tục theo thời gian thực. Khi số giờ còn lại tới Deadline giảm xuống, điểm số tăng theo hàm phi tuyến tính, tự động đẩy công việc lên vị trí số 1 và đổi màu cảnh báo. |
| **Khuất mắt trông coi (Out of sight, out of mind):** Đóng hoặc thu nhỏ trình duyệt là quên ngay danh sách việc cần làm. | **Always-on-Top Sticky Mini HUD (Document PiP API):** Tách một cửa sổ nhỏ luôn nổi trên mọi ứng dụng Windows/macOS ngay từ trình duyệt mà không cần cài phần mềm Desktop. Luôn hiển thị Top 3 task khẩn cấp nhất và đồng hồ Pomodoro. |
| **Tách rời giữa Quản lý việc và Học tập:** Các app To-do không hiểu khái niệm Môn học, Đề cương, Kỳ thi hay Ôn tập ngắt quãng. | **Dedicated Study Workspace:** Chuyển đổi tài liệu/đề cương môn học thành các Topic nhỏ, tự động lập lịch ôn tập lặp lại ngắt quãng (Spaced Repetition 1-3-7-14-30 ngày) và tính toán khối lượng bài cần học mỗi ngày để kịp ngày thi. |
| **Trải nghiệm rời rạc giữa các khía cạnh cuộc sống:** Nhật ký không biết hôm nay bạn đã hoàn thành những task nào hay đã tiêu bao nhiêu tiền. | **Cross-Module Context Linking:** Trang Nhật ký cuối ngày tự động đính kèm tóm tắt: số task đã hoàn thành, số giờ đã học, thói quen đã tick và tổng tiền đã chi trong ngày đó. |

---

## 4. Phạm Vi Sản Phẩm (Product Scope)

### 4.1. Các phân hệ NẰM TRONG phạm vi (In-Scope)

1. **Xác thực & Hồ sơ cá nhân (Auth & Profile):**
   - Đăng ký/Đăng nhập Email + Mật khẩu.
   - Đăng nhập Google OAuth2 và tự động gộp tài khoản (Account Linking).
   - Cài đặt giao diện (Dark/Light Mode), múi giờ, công thức trọng số tính điểm ưu tiên.

2. **Lịch Biểu & Động Cơ Công Việc (Smart Calendar & Dynamic Tasks):**
   - Lịch tuần/tháng trực quan, kéo thả khung giờ làm việc (Time-blocking).
   - Đồng bộ sự kiện từ Google Calendar.
   - Quản lý Task (CRUD, Subtasks, Tags, Base Priority, Estimated Effort, Deadline).
   - Tự động tính điểm Urgency Score và sắp xếp bảng xếp hạng công việc.

3. **Cửa Sổ Nổi Nhắc Việc (Always-on-Top Mini HUD):**
   - Khởi chạy cửa sổ Document Picture-in-Picture luôn nổi trên màn hình máy tính.
   - Đồng hồ Pomodoro (Focus / Short Break / Long Break) đồng bộ thời gian thực với phiên học/làm việc.
   - Đánh dấu hoàn thành Task và điều khiển nhạc nền ngay trên cửa sổ nổi.

4. **Không Gian Học Tập Chuyên Sâu (Study Workspace):**
   - Quản lý Học kỳ (Semesters), Môn học (Courses), Lịch học cố định trên trường (Class Timetable).
   - Quản lý Tài liệu học tập (Study Materials - hỗ trợ import file PDF/DOCX/Text để bóc tách danh sách chương mục thành các Study Units).
   - Theo dõi tiến độ học mới và lịch ôn lại bài cũ theo thuật toán Spaced Repetition.
   - Quản lý Kỳ thi (Exams): Đếm ngược ngày thi, checklist kiến thức trọng tâm và chỉ số sẵn sàng (Readiness Score %).

5. **Quản Lý Tài Chính Cá Nhân (Finance Ledger):**
   - Quản lý Ví (Wallets), Danh mục Thu/Chi (Categories), Giao dịch (Transactions).
   - Thiết lập Ngân sách tháng (Monthly Budgets) và thanh cảnh báo chi tiêu vượt ngưỡng.
   - Thống kê dòng tiền ròng và cơ cấu chi tiêu theo tháng.

6. **Theo Dõi Thói Quen & Thể Thao (Habit & Fitness Tracker):**
   - Thiết lập mục tiêu thói quen và bài tập thể thao (tần suất hàng ngày hoặc theo các thứ trong tuần, định lượng mục tiêu như 2000ml nước, 5km chạy bộ).
   - Ghi nhận kết quả hàng ngày (Check-in), tính chuỗi Streak và hiển thị biểu đồ nhiệt 365 ngày.

7. **Nhật Ký & Cảm Xúc (Daily Journal & Mood):**
   - Viết nhật ký định dạng Markdown theo từng ngày.
   - Chấm điểm cảm xúc (Mood 1-5) và chọn nhãn trạng thái tinh thần.
   - Tự động tổng hợp hoạt động trong ngày (Daily Snapshot) đính kèm vào trang nhật ký.

8. **Trình Phát Nhạc Tập Trung (Ambient Focus Player):**
   - Phát nhạc Lo-Fi và trộn các hiệu ứng âm thanh nền (Mưa, Sóng biển, Quán cà phê, Lửa trại) với thanh trượt âm lượng độc lập.

### 4.2. Các tính năng ĐÃ LƯỢC BỎ khỏi phạm vi (Out-of-Scope)
- **Không** xây dựng tính năng theo dõi thời gian sử dụng các ứng dụng/mạng xã hội bên thứ ba (Facebook, TikTok, YouTube) để đảm bảo quyền riêng tư và không phụ thuộc vào Chrome Extension ngoài.
- **Không** tích hợp widget dự báo thời tiết để giữ giao diện tập trung hoàn toàn vào dữ liệu nội tại của người dùng.
- **Không** tích hợp cổng thanh toán tự động với ngân hàng thật (sử dụng cơ chế ghi nhận giao dịch chủ động và nhanh chóng qua Command Palette).

---

## 5. Chỉ Số Đo Lường Thành Công Của Sản Phẩm (Product Metrics)

1. **Hiệu năng phản hồi giao diện:** Mọi thao tác chuyển tab, mở Command Palette (`Ctrl + K`), tick hoàn thành task phản hồi dưới `100ms` (nhờ Optimistic UI Update).
2. **Độ chính xác của Động cơ Điểm Khẩn Cấp:** 100% các task có hạn chót dưới 24 giờ phải tự động vượt lên nhóm đầu danh sách và kích hoạt trạng thái cảnh báo trực quan.
3. **Tính ổn định của Mini HUD:** Cửa sổ Document PiP duy trì kết nối trạng thái hai chiều với ứng dụng chính liên tục trong các phiên làm việc kéo dài 2 - 4 giờ mà không bị rò rỉ bộ nhớ (Memory Leak).
4. **Độ toàn vẹn dữ liệu tài chính và lịch ôn tập:** 0% sai lệch số dư ví khi thực hiện nhiều giao dịch đồng thời; 100% các mục kiến thức sau khi đánh giá mức độ nhớ đều được tính đúng ngày ôn tập tiếp theo.
