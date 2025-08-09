---
title : "Giới thiệu"
date: 2025-07-16
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---

## Tổng quan Workshop

Workshop này hướng dẫn cách xây dựng **Feature Store tái sử dụng** cho quy trình huấn luyện mô hình Machine Learning bằng cách sử dụng **Amazon SageMaker Feature Store** (chế độ Offline). Bạn sẽ học cách:
- Tải dữ liệu từ S3
- Xử lý đặc trưng bằng Pandas
- Đăng ký và lưu đặc trưng trong Feature Store
- Huấn luyện mô hình XGBoost từ các đặc trưng đã lưu


## Vì sao cần Feature Store?

**Feature engineering** là bước tốn nhiều thời gian và dễ lặp lại trong pipeline huấn luyện ML. Khi thiếu nơi lưu trữ đặc trưng chung, các nhóm thường:
- Lặp lại xử lý giống nhau
- Dễ sai schema, lỗi data leakage
- Tốn công bảo trì và kiểm thử
- Triển khai chậm do thiếu đồng bộ

### Lợi ích của Feature Store:
- **Tái sử dụng đặc trưng** giữa các pipeline và team
- **Tăng tốc huấn luyện** nhờ chuẩn hóa schema
- **Giảm duplication** và công sức bảo trì
- **Hỗ trợ traceability** và audit ML pipeline


## Kiến trúc Hệ thống

### Luồng xử lý đặc trưng:
CSV → S3 → Pandas Script → Feature Store (Offline Only)
→ SageMaker Training (XGBoost) → Model Artifact
![Picture1](/images/Sodo.png)

### Các dịch vụ chính:

#### 1. **Amazon S3**
- Lưu trữ dữ liệu CSV gốc
- Kết nối trực tiếp với Pandas

#### 2. **Pandas + Python**
- Đọc dữ liệu từ S3
- Xử lý các bước:
  - Làm sạch (null handling, filtering)
  - Encode biến phân loại (LabelEncoding, OneHot)
  - Feature scaling (MinMax/Standard)
- Ghi dữ liệu đặc trưng vào Feature Store thông qua SDK

#### 3. **SageMaker Feature Store (Offline)**
- Lưu trữ đặc trưng theo thời gian (`event_time`)
- Tạo theo `FeatureGroup` với schema cố định
- Dữ liệu được ghi vào S3 dưới dạng Parquet
- Cho phép truy vấn lại đặc trưng để huấn luyện hoặc kiểm thử

#### Ưu điểm:
- **Tách biệt với code xử lý**
- **Có thể chia sẻ giữa các pipeline**
- **Tái sử dụng – không cần xử lý lại**

#### 4. **SageMaker Training Job**
- Sử dụng dữ liệu từ Feature Store thông qua query
- Train mô hình XGBoost
- Lưu mô hình sau huấn luyện vào S3 (dưới dạng `.tar.gz`)
- Có thể deploy hoặc đánh giá tiếp tục

## Mục tiêu cuối cùng

Workshop sẽ giúp bạn:

- Tái sử dụng đặc trưng ở nhiều mô hình
- Xây dựng pipeline training nhất quán
- Áp dụng được vào bài toán sản phẩm thực tế
- Sẵn sàng tích hợp với SageMaker Pipelines về sau

## Chuẩn bị Workshop

Trước khi bắt đầu, hãy đảm bảo bạn có:
- **AWS Account** có thể tạo được các dịch vụ sau:
  - Amazon S3
  - Amazon SageMaker
  - IAM Roles
- Nếu là tài khoản mới: nên **kích hoạt Free Tier**
- **Quyền IAM tối thiểu**:
  - `AmazonS3FullAccess` (hoặc gắn chính xác bucket)
  - `AmazonSageMakerFullAccess`
  - `IAMPassRole` (nếu tạo role cho notebook/training)

{{% notice info %}}
Workshop này được thiết kế để chạy trong **AWS Free Tier**. Tổng chi phí ước tính < $1 USD nếu thực hiện đúng hướng dẫn.
{{% /notice %}}