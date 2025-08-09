---

title : "Đánh giá và hiệu quả"
date: 2025-07-16
weight : 4
chapter : false
pre : " <b> 4. </b> "
---------------------

## 📈 Đánh giá mô hình và hiệu quả pipeline
Phần này giúp bạn đánh giá kết quả sau khi huấn luyện mô hình với đặc trưng lấy từ SageMaker Feature Store. Chúng ta sẽ xem xét:

* Độ chính xác của mô hình (accuracy, F1-score...)
* Khả năng tái sử dụng đặc trưng
* Tốc độ huấn luyện
* Lợi ích so với quy trình truyền thống

---

## 1. Đánh giá độ chính xác mô hình
Sau khi train mô hình, bạn có thể đánh giá bằng tập kiểm tra (`test set`) hoặc sử dụng cross-validation.

### 🎯 Các chỉ số phổ biến:
* **Accuracy**: Tỷ lệ dự đoán đúng
* **Precision/Recall**: Đặc biệt quan trọng với churn prediction
* **F1-score**: Trung bình điều hòa giữa precision và recall
* **AUC-ROC**: Khả năng phân biệt giữa churn vs non-churn

### 🧪 Ví dụ đánh giá:
```python
from sklearn.metrics import classification_report, roc_auc_score

y_true = test_labels
y_pred = model.predict(test_data)

print(classification_report(y_true, y_pred))
print("AUC:", roc_auc_score(y_true, y_pred))
```

### 🎯 Kỳ vọng:
* Accuracy > **85%**
* AUC > **0.90** (tuỳ vào domain và dữ liệu)
---

## 2. Khả năng tái sử dụng đặc trưng (Feature Reuse)
### ✅ Mục tiêu:
Tái sử dụng FeatureGroup đã có thay vì xử lý lại đặc trưng từ đầu.

### 🔄 Kịch bản:
* Huấn luyện mô hình khác (LightGBM, Logistic...) trên cùng FeatureGroup
* Tái huấn luyện (retraining) định kỳ
* Dùng đặc trưng trong batch inference hoặc prediction API

### 🔁 Cách kiểm tra reuse:
* So sánh số lượng FeatureGroup đã dùng hơn 1 lần
* Thống kê % reuse của feature definitions giữa các mô hình

```python
used_features = ["monthly_charges", "tenure"]
reused = sum([f in previous_models_used for f in used_features])
print("Reuse rate:", reused / len(used_features))
```

---

## 3. Tăng tốc thời gian huấn luyện
Việc lưu đặc trưng giúp:
* Tránh bước xử lý Pandas lặp lại
* Truy vấn nhanh với Athena hoặc Glue

### 📊 So sánh thời gian:

| Lần huấn luyện  | Thời gian xử lý đặc trưng | Tổng thời gian |
| --------------- | ------------------------- | -------------- |
| Lần đầu         | \~5 phút                  | \~10 phút      |
| Lần sau (reuse) | < 1 phút                  | \~5 phút       |

→ **Giảm 30–50%** tổng thời gian huấn luyện cho mỗi vòng lặp

---

## 4. So sánh với quy trình truyền thống
| Tiêu chí          | Không dùng Feature Store | Dùng Feature Store            |
| ----------------- | ------------------------ | ----------------------------- |
| Thời gian xử lý   | Lặp lại mỗi lần          | Reuse được                    |
| Độ đồng nhất      | Không đảm bảo            | Chuẩn hóa theo schema         |
| Dễ bảo trì        | Mỗi team xử lý riêng     | Có nơi lưu trữ chung          |
| Tích hợp pipeline | Khó                      | Dễ kết nối SageMaker          |
| Khả năng mở rộng  | Giới hạn                 | Dễ scale batch hoặc real-time |

## 5. Tổng kết lợi ích thu được

### 🎯 Từ góc nhìn kỹ thuật:
* ✅ Giảm duplication và sai lệch logic
* ✅ Tái sử dụng đặc trưng dễ dàng
* ✅ Tích hợp Athena, Glue, Training job dễ dàng

### 💼 Từ góc nhìn doanh nghiệp:
* ⏱️ Tăng tốc time-to-model
* 💰 Giảm chi phí khi scale
* 📊 Nâng cao chất lượng dữ liệu và tính reproducible

---

{{% notice tip %}}
Bạn có thể thêm custom logging hoặc tạo bảng theo dõi reuse/training accuracy để tích hợp vào dashboard nội bộ.
{{% /notice %}}
