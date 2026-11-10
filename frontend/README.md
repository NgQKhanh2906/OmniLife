# OmniLife Frontend - Next.js 14 App Router & Document PiP HUD

Thư mục này chứa toàn bộ mã nguồn giao diện người dùng của hệ thống **OmniLife**, được phát triển trên nền tảng **Next.js 14 (App Router)**, **React 18**, **TypeScript** và **TailwindCSS**.

## 1. Cấu Trúc Thư Mục Chính

- `public/audio/`: Chứa các tệp âm thanh nhạc nền Lo-Fi (`lofi/`), âm thanh trắng môi trường lặp liền mạch (`ambient/`) và hiệu ứng tương tác (`sfx/`).
- `public/images/`: Chứa biểu tượng nhận diện thương hiệu SVG, hình minh họa trạng thái trống (Empty States) và ảnh đại diện mặc định.
- `src/app/(auth)/`: Nhóm trang đăng nhập, đăng ký và xử lý callback Google OAuth2.
- `src/app/(workspace)/`: Nhóm các không gian làm việc chính (Dashboard, Calendar, Study Workspace, Finance Ledger, Habits & Fitness, Daily Journal, Settings) đặt trong bố cục Bento Grid Shell.
- `src/components/hud/`: Thành phần cốt lõi khởi tạo và đồng bộ cửa sổ nổi Always-on-Top Mini HUD qua chuẩn `window.documentPictureInPicture` và `ReactDOM.createPortal`.
- `src/components/`: Các thành phần giao diện phân chia theo từng phân hệ nghiệp vụ (`layout`, `productivity`, `study`, `finance`, `habit`, `journal`, `ui`).
- `src/hooks/`, `src/stores/`, `src/lib/`, `src/types/`: Quản lý vòng đời đồng hồ Pomodoro, bộ trộn âm thanh đa luồng, Zustand Store, Axios Interceptor và định nghĩa kiểu TypeScript.

## 2. Tài Liệu Tham Chiếu

- Quy chuẩn hệ thống thiết kế (Design System): [../docs/design-system.md](../docs/design-system.md)
- Tài nguyên tĩnh và dữ liệu mẫu: [../docs/assets.md](../docs/assets.md)
- Cấu trúc thư mục chi tiết: [../docs/folder-structure.md](../docs/folder-structure.md)
