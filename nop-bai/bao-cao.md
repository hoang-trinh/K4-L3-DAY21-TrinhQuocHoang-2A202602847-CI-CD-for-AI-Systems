# Báo Cáo Lab Day 21 - CI/CD cho AI Systems

| | |
|---|---|
| Họ và tên | Trịnh Quốc Hoàng |
| MSSV | 2A202602847 |
| Lớp / Khóa | K4 |
| Repo GitHub | https://github.com/hoang-trinh/K4-L3-DAY21-TrinhQuocHoang-2A202602847-CI-CD-for-AI-Systems |
| Ngày nộp | 07/10/2026 |

---

## 1. Bộ Siêu Tham Số Đã Chọn và Lý Do

| Lần chạy | n_estimators | learning_rate | max_depth | f1_score | accuracy |
|---|---|---|---|---|---|
| 1 | 100 | 0.1 | 3 | 0.7109 | 0.8780 |
| 2 | 50 | 0.05 | 2 | 0.6051 | 0.8460 |
| 3 | 200 | 0.1 | 5 | 0.7149 | 0.8740 |

**Bộ siêu tham số đã chọn:** `n_estimators=200`, `learning_rate=0.1`, `max_depth=5`.

**Lý do:** Bộ siêu tham số ở lần chạy 3 mang lại hiệu năng nhận diện lớp thiểu số tốt nhất với f1_score đạt 0.7149, vượt trội hơn lần 1 (0.7109) và lần 2 (0.6051). Đáng chú ý, lần chạy có accuracy cao nhất là lần 1 (0.8780) chứ không phải lần 3 (0.8740). Sự chênh lệch này cho thấy mô hình lần 1 tối ưu độ chính xác tổng thể bằng cách dự đoán thiên về lớp đa số, trong khi mô hình lần 3 học sâu hơn các đặc trưng phức tạp của lớp thu nhập cao. Giữa n_estimators và learning_rate có sự đánh đổi rõ rệt: ở lần 2 khi giảm learning_rate xuống 0.05 với 50 cây nông, mô hình bị underfit nghiêm trọng khiến f1_score tụt xuống 0.6051; ngược lại khi tăng lên 200 cây với độ sâu 5 ở lần 3, các cây bù trừ sai số hiệu quả giúp f1_score vượt xa ngưỡng chất lượng 0.65.

---

## 2. Vì Sao Ngưỡng Chất Lượng Đặt Trên F1 Chứ Không Phải Accuracy

Tập dữ liệu Adult có phân bố lớp mất cân bằng nghiêm trọng khi chỉ có 24.8% số mẫu thuộc lớp thu nhập trên 50K USD. Một mô hình cơ sở tầm thường luôn gán nhãn thu nhập thấp cho toàn bộ các mẫu vẫn sẽ đạt accuracy giả tạo lên tới 75.2%, nhưng thực tế hoàn toàn vô dụng vì f1_score của lớp dương bằng 0. Accuracy chỉ phản ánh tỷ lệ dự đoán đúng trên toàn bộ tập dữ liệu nên bị lớp đa số áp đảo hoàn toàn, che lấp sai sót trong việc phân loại nhóm người thu nhập cao. F1-score của lớp dương là trung bình điều hòa giữa Precision và Recall, đo lường chính xác năng lực phát hiện lớp thiểu số quan trọng mà không bị sai lệch bởi lớp chiếm đa số. Khi gọi f1_score, ta tuyệt đối không dùng tham số average="macro" hay average="weighted" vì việc tính trung bình trọng số sẽ khiến lớp đa số 75.2% kéo điểm số lên cao, làm lu mờ hiệu quả thực nghiệm và vô hiệu hóa ý nghĩa kiểm soát của Quality Gate.

---

## 3. Khó Khăn Gặp Phải và Cách Giải Quyết

| Khó khăn | Nguyên nhân | Cách giải quyết |
|---|---|---|
| Lỗi import `FallbackAsyncAdaptedQueuePool` khi khởi tạo tracking MLflow | Thư viện SQLAlchemy phiên bản mới loại bỏ class này khỏi `sqlalchemy.pool` gây xung đột với MLflow 2.13.0 | Gán alias tương thích `FallbackAsyncAdaptedQueuePool = AsyncAdaptedQueuePool` trực tiếp vào `sqlalchemy.pool` trước khi import MLflow |
| Nguy cơ runner GitHub Actions thiếu dữ liệu nhị phân khi chạy `dvc pull` | Đẩy commit Git chứa con trỏ `.dvc` lên remote trước khi hoàn tất đẩy dữ liệu lên Cloud Storage | Luôn thực hiện lệnh `dvc push` đồng bộ dữ liệu lên bucket trước khi thực hiện `git push` lên GitHub |
| Lỗi xác thực quyền truy cập Cloud Storage khi kéo và đẩy dữ liệu DVC | Service Account chưa được gán đúng vai trò hoặc đường dẫn file khóa chứng thực bị sai lệch | Cấp đúng quyền `roles/storage.objectAdmin` trên bucket và cấu hình biến môi trường `credentialpath` cho remote DVC |

---

## 4. So Sánh Bước 2 và Bước 3 (bắt buộc, 2 - 3 câu)

| | f1_score | accuracy |
|---|---|---|
| Bước 2 (chỉ `train_batch1`) | 0.7149 | 0.8740 |
| Bước 3 (thêm `train_batch2`) | ___ | ___ |

**Nhận xét:** ___

---

## 5. Phần Bonus Đã Thực Hiện (nếu có)

- [ ] Bonus 1 - Tracking MLflow từ xa với DagsHub: ___
- [ ] Bonus 2 - Điều chỉnh ngưỡng quyết định: ___
- [ ] Bonus 3 - Báo cáo precision / recall tự động: ___
- [ ] Bonus 4 - Hoàn trả về phiên bản trước: ___
- [ ] Bonus 5 - Cảnh báo lệch lạc dữ liệu: ___
