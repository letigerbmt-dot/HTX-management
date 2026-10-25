# HTX Management System (Hệ thống Quản trị Điều hành và Quản lý Hợp tác xã)

> **Trạng thái:** Khởi tạo kiến trúc nền tảng (Foundation Architecture)  
> **Phiên bản tài liệu:** 2.0 (Dựa trên `docs/PRD_HTX_v2.docx`)  
> **Giai đoạn hiện tại:** Phase G0 – Thiết lập kiến trúc, quản trị dữ liệu và đặc tả kỹ thuật (Chưa lập trình tính năng)

---

## 1. Giới thiệu và Mục tiêu Hệ thống

**HTX Management System** là hệ thống phần mềm web chuyên nghiệp phục vụ công tác quản trị, điều hành nội bộ và quản lý mạng lưới hơn **1.000 Hợp tác xã (HTX)** trên địa bàn **102 xã/phường**.

### Mục tiêu cốt lõi:
1. **Nguồn dữ liệu thống nhất (Single Source of Truth):** Tập trung hóa toàn bộ dữ liệu về tổ chức, 23 nhân sự, danh mục 102 xã/phường, hồ sơ >1.000 HTX, kế hoạch công tác, nhiệm vụ, KPI, văn bản và các chương trình hỗ trợ.
2. **Chuẩn hóa quy trình điều hành khép kín:** Kết nối liên tục không đứt gãy: *Kế hoạch công tác $\rightarrow$ Giao việc/nhiệm vụ $\rightarrow$ Nghiệm thu kết quả $\rightarrow$ Đánh giá KPI $\rightarrow$ Báo cáo phân tích*.
3. **Phê duyệt tập trung & Kiểm soát thẩm quyền nghiêm ngặt:** Phê duyệt kế hoạch và chỉ tiêu KPI tập trung ở cấp Lãnh đạo cơ quan (Chủ tịch, các Phó Chủ tịch theo lĩnh vực/ủy quyền). Phòng chuyên môn tuyệt đối không tự phê duyệt.
4. **Quản trị chất lượng dữ liệu thích ứng (Adaptive Data Quality):** Cho phép tiếp nhận dữ liệu HTX ban đầu chưa hoàn thiện từ các tệp Excel (chưa bắt buộc mã số thuế, mã địa bàn), qua cơ chế đối soát so trùng thông minh (không tự động gộp), chuẩn hóa và nâng dần độ tin cậy tới khi xác minh chính thức.
5. **Cơ chế ủy quyền nghiệp vụ có thời hạn:** Cho phép lãnh đạo ủy quyền giải quyết công việc theo đúng phạm vi, lĩnh vực, thời gian hiệu lực và lưu vết kiểm toán (audit log) bất biến.

---

## 2. Bối cảnh Vận hành & Ràng buộc Cốt lõi

- **Quy mô nhân sự cơ quan:** 23 nhân sự, gồm:
  - **Lãnh đạo cơ quan (04 người):** 01 Chủ tịch, 03 Phó Chủ tịch.
  - **Văn phòng (09 người):** Công tác tổng hợp, tổ chức cán bộ, văn thư, hậu cần, lịch công tác.
  - **Phòng Chính sách – Phát triển (10 người):** Nghiệp vụ quản lý HTX, địa bàn, hỗ trợ, đào tạo, thẩm định chính sách.
- **Quy mô đối tượng quản lý:** Hơn 1.000 HTX tại 102 xã/phường.
- **Ràng buộc công nghệ & pháp lý:**
  - **Không sử dụng chữ ký số:** Cơ chế phê duyệt là workflow điện tử nội bộ, gắn liền với tài khoản, mật khẩu, MFA và audit trail; không tạo giao diện hoặc quy trình giả định chữ ký số công cộng.
  - **Thẩm quyền phê duyệt:** Lãnh đạo phòng chỉ kiểm tra, rà soát và trình duyệt; không có quyền phê duyệt kế hoạch/KPI.
  - **Lịch sử và bảo toàn dữ liệu:** Không xóa vật lý (`no hard-delete`) các bản ghi đã phát sinh quan hệ nghiệp vụ; sử dụng soft-delete và lưu vết lịch sử phiên bản (`row_version`).
  - **Thời gian & Định dạng:** Lưu trữ `UTC` tại cơ sở dữ liệu, hiển thị múi giờ `Asia/Ho_Chi_Minh` (UTC+07:00), định dạng ngày `dd/MM/yyyy`, định dạng số/tiền tệ theo chuẩn Việt Nam (`vi-VN`).

---

## 3. Danh mục 15 Module Chức năng (M01 – M15)

Hệ thống được thiết kế theo mô hình Modular Monolith, chia làm 3 nhóm ưu tiên phát triển:

| Mã | Tên Module | Ưu tiên | Route gốc | Vai trò chính |
| :--- | :--- | :---: | :--- | :--- |
| **M01** | Dashboard – Điều hành | **P0** | `/dashboard` | Tổng hợp chỉ số điều hành, drill-down số liệu, cảnh báo quá hạn. |
| **M02** | Tổ chức và Nhân sự | **P0** | `/organization` | Sơ đồ tổ chức, 23 hồ sơ nhân sự, chức vụ, điều chuyển, bảo vệ thông tin nhạy cảm. |
| **M03** | Kế hoạch công tác | **P0** | `/plans` | Lập kế hoạch năm/quý/tháng/tuần, trình duyệt tập trung, quản lý phiên bản (revision). |
| **M04** | Công việc và Nhiệm vụ | **P0** | `/tasks` | Giao việc, theo dõi tiến độ, báo cáo kết quả, quy trình nghiệm thu bắt buộc. |
| **M05** | KPI và Đánh giá | **P0** | `/kpi` | Thiết kế thư viện KPI, giao chỉ tiêu, chốt minh chứng từ task đã nghiệm thu, khóa kỳ. |
| **M06** | Văn bản và Phê duyệt | **P1** | `/documents` | Quản lý sổ văn bản đến/đi, phân luồng xử lý, phê duyệt nội bộ, gắn với nhiệm vụ. |
| **M07** | Hồ sơ Hợp tác xã | **P0** | `/cooperatives` | Hồ sơ 360° HTX, import Excel qua staging, đối soát trùng lặp, cấp mã nội bộ, xác minh. |
| **M08** | Địa bàn và Phụ trách | **P0** | `/territories` | Quản lý danh mục 102 xã/phường, phân công cán bộ phụ trách, theo dõi biến động địa bàn. |
| **M09** | Yêu cầu và Hoạt động hỗ trợ | **P1** | `/support` | Tiếp nhận, phân loại, giao xử lý kiến nghị của HTX, ghi nhận kết quả và đánh giá. |
| **M10** | Đào tạo và Hội nghị | **P1** | `/training` | Tổ chức lớp bồi dưỡng, hội nghị, điểm danh HTX tham gia, khảo sát chất lượng. |
| **M11** | Chương trình/Chính sách hỗ trợ | **P1** | `/programs` | Thiết lập tiêu chí, tiếp nhận hồ sơ, thẩm định phê duyệt kinh phí hỗ trợ HTX. |
| **M12** | Báo cáo và Phân tích | **P0** | `/reports` | Báo cáo động, thống kê theo kỳ/địa bàn/ngành nghề, snapshot số liệu, xuất Excel/PDF. |
| **M13** | Lịch và Thông báo | **P1** | `/calendar` | Lịch công tác cơ quan, lịch họp, nhắc việc tự động, trung tâm thông báo đa kênh. |
| **M14** | Quản trị và Bảo mật | **P0** | `/admin` | Quản trị tài khoản, RBAC + ABAC data scope, cấu hình ủy quyền lãnh đạo, audit log. |
| **M15** | Cổng Hợp tác xã (Portal) | **P2** | `/portal` | Kênh tự phục vụ cho HTX: cập nhật đề xuất hồ sơ, gửi kiến nghị hỗ trợ, đăng ký lớp học. |

---

## 4. Kiến trúc Công nghệ Định hướng

- **Kiến trúc tổng thể:** Modular Monolith (Clean Architecture / Domain-Driven Design tổ chức theo module).
- **Frontend:** Next.js (App Router), React, TypeScript, Ant Design, TailwindCSS.
- **Backend:** NestJS, TypeScript, RESTful API với tài liệu OpenAPI/Swagger, DTO Validation.
- **Cơ sở dữ liệu:** PostgreSQL (hỗ trợ JSONB, relational integrity, foreign keys, partition nếu cần).
- **ORM & Migrations:** Prisma Client & Prisma Migrate.
- **Hàng đợi & Cache:** Redis + BullMQ (xử lý background import Excel, export báo cáo, notification queue).
- **Lưu trữ tệp:** S3-compatible Object Storage (phân quyền download thông qua Signed URL).
- **Bảo mật:** OAuth2/OIDC, JWT Access/Refresh tokens, MFA cho tài khoản đặc quyền, chính sách API deny-by-default.

---

## 5. Tài liệu Dự án

Tất cả tài liệu kỹ thuật và thiết kế chi tiết được lưu trữ tại thư mục `docs/`:
- [`docs/PROJECT_CONTEXT.md`](file:///c:/WEBAPP/DHTX-Management/docs/PROJECT_CONTEXT.md): Bối cảnh chi tiết, cơ cấu bộ máy 23 người, ma trận 15 module và các ràng buộc đặc thù.
- [`docs/DECISIONS.md`](file:///c:/WEBAPP/DHTX-Management/docs/DECISIONS.md): Toàn bộ các quyết định kiến trúc, công nghệ và nghiệp vụ đã được chốt (ADRs).
- [`docs/OPEN_QUESTIONS.md`](file:///c:/WEBAPP/DHTX-Management/docs/OPEN_QUESTIONS.md): Danh mục câu hỏi mở, các điểm cần xin ý kiến và chờ Lãnh đạo cơ quan phê duyệt trước khi lập trình.
- `docs/PRD_HTX_v2.docx`: Văn bản đặc tả yêu cầu sản phẩm gốc (Phiên bản 2.0).
"# HTX-management" 
