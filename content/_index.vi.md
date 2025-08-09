---
title : "Feature Store ML"
date: 2025-07-16
weight : 1 
chapter : false
---
# Xây Dựng Feature Store Tái Sử Dụng Cho Huấn Luyện Mô Hình ML Bằng SageMaker

### Tổng quan

- Feature engineering (xử lý đặc trưng) là một trong những bước tốn thời gian nhất trong quy trình huấn luyện mô hình Machine Learning. Trong nhiều doanh nghiệp, các đội nhóm ML thường xử lý lại những đặc trưng tương tự do không có kho lưu trữ trung tâm và công cụ reuse. Việc này dẫn đến tính trạng không đồng nhất, chi phí tăng và lãng phí nỗ lực.
- Dự án này đề xuất một cách tiếp cận đơn giản nhưng thực tiễn để xây dựng Feature Store bằng Amazon SageMaker Feature Store. Mục tiêu là cho phép lưu trữ và tái sử dụng đặc trưng cho huấn luyện mô hình và retraining một cách đồng nhất, tiết kiệm và đúng quy trình. Bài toán chính áp dụng là Customer Churn Prediction với tập dữ liệu công khai. Các thành phần bao gồm: script xử lý bằng Pandas, SageMaker Feature Store (chỉ Offline), và training bằng XGBoost.
- Kết quả mong muốn: tăng reuse, giảm duplication, tăng tốc training, và nâng cao chất lượng pipeline ML.

![Feature-Store-ML](/images/trangchinh.png)
### Nội dung

 1. [Giới thiệu](1-introduce/)
 2. [Các bước chuẩn bị](2-Prerequiste/)
 3. [Các bước thực hiện](3-Implementation_steps/).
 4. [Đánh giá và hiệu quả](4-Evaluation_Effectiveness/)
 5. [Dọn dẹp tài nguyên](5-cleanup/)