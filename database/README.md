# OmniLife Database - MySQL 8.0 & MySQL Workbench Artifacts

Thư mục này lưu trữ mô hình thiết kế cơ sở dữ liệu thực thể quan hệ (EER Diagram) trên **MySQL Workbench 8.0** và các kịch bản SQL phục vụ kiểm thử, đo lường hiệu năng truy vấn trên **MySQL 8.0 InnoDB**.

## 1. Danh Mục Tệp Thiết Kế & Kiểm Thử (Theo `docs/folder-structure.md`)

- `omnilife_erd_model.mwb`: Tệp mô hình hóa sơ đồ thực thể quan hệ 24 bảng mở trực tiếp bằng MySQL Workbench.
- `explain_queries_benchmark.sql`: Tập hợp các câu lệnh `EXPLAIN FORMAT=JSON` dùng để kiểm tra kế hoạch thực thi truy vấn (Query Execution Plan) cho bảng xếp hạng công việc khẩn cấp, lịch biểu hợp nhất và sổ cái tài chính.
- `index_tuning_verification.sql`: Kịch bản kiểm chứng tốc độ truy vấn trước và sau khi áp dụng 8 Composite & Covering Indexes.

## 2. Lưu Ý Về Flyway Migration

Các kịch bản khởi tạo cấu trúc bảng tự động (`V1__init_omnilife_schema.sql`) và nạp dữ liệu mẫu (`V2__seed_initial_data.sql`) khi chạy ứng dụng Spring Boot được đặt tại thư mục `../backend/src/main/resources/db/migration/`.

Xem đặc tả chi tiết toàn bộ 24 bảng và chiến lược đánh chỉ mục tại [../docs/database.md](../docs/database.md).
