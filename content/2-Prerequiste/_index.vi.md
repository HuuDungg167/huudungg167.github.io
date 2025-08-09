---
title : "Các bước chuẩn bị"
date: 2025-06-18
weight : 2
chapter : false
pre : " <b> 2. </b> "
---

## Tổng quan Chuẩn bị

Để thực hiện workshop thành công, bạn cần chuẩn bị đầy đủ các điều kiện tiên quyết về tài khoản **AWS**, môi trường làm việc, và kiến thức cơ bản. Phần này sẽ hướng dẫn chi tiết từng bước chuẩn bị.

## 1. Tài khoản AWS

### Yêu cầu Tài khoản:
- **AWS Account** đã kích hoạt và xác thực đầy đủ
- **Credit card** đã đăng ký (chỉ dùng cho xác minh, không bị tính phí trong Free Tier)
- **S3 & SageMaker** phải hoạt động ở Region hỗ trợ (ví dụ: `ap-southeast-1`)

### AWS Free Tier Coverage:
Workshop này được thiết kế để chạy hoàn toàn trong **AWS Free Tier**:

| Dịch vụ                | AWS Free Tier                 | Sử dụng trong workshop      |
|------------------------|-------------------------------|-----------------------------|
| **S3**                 | 5GB, 20.000 GET/PUT           | < 500MB, vài trăm requests  |
| **SageMaker Notebook** | 250 giờ t3.medium/tháng       | ~1–2 giờ                    |
| **Training Job**       | Không Free Tier               | Dùng instance nhỏ + Spot    |
| **Feature Store (Offline)** | Không có trong Free Tier   | ~10–15 MB dữ liệu đặc trưng |

{{% notice info %}}
**Ước tính chi phí**: ~$0 nếu dùng Notebook, < $1–2 USD nếu sử dụng Training job và lưu Feature Store ở mức nhỏ.
{{% /notice %}}

### Thiết lập IAM và Quyền truy cập :

Để thực hiện workshop, tài khoản **AWS** của bạn cần có các permissions sau:

#### **Quyền IAM tối thiểu:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "sagemaker:*",
        "s3:*",
        "iam:PassRole"
      ],
      "Resource": "*"
    }
  ]
}
```

#### **Khuyến nghị:**
- Dùng AdministratorAccess trong môi trường học/lab.
- Không dùng root account để thao tác.
- Gắn IAM Role vào SageMaker notebook instance nếu sử dụng Studio hoặc Classic Notebook.

## 2. Môi trường Làm việc

### Trình duyệt:
- **Modern browser**: Chrome, Firefox, Safari bản mới nhất
- **JavaScript enabled**
- **Cookies enabled** cho **AWS Console**
- **Pop-up blocker disabled** cho AWS domains

### Kết nối mạng:
- **Stable internet connection** ( ≥ 2 Mbps)
- **Access to AWS endpoints** (không bị firewall block)
- **HTTPS support** (TLS 1.2+)

### Công cụ cần thiết:
| Công cụ | Yêu cầu tối thiểu                       |
| ------- | --------------------------------------- |
| Python  | >= 3.10                                 |
| pip     | Cài gói: `pandas`, `boto3`, `sagemaker` |
| Jupyter | Khuyến khích dùng Jupyter Notebook      |
| Git     | Để clone project (nếu có)               |

```
pip install pandas boto3 sagemaker
```
AWS CLI (tuỳ chọn):
```
aws configure
```

## 3. Kiến thức Cơ bản

### 1. Tổng quan về AWS cho ML

#### **AWS Services Overview:**
| Dịch vụ          | Vai trò chính trong workshop |
|------------------|------------------------------|
| **Amazon S3**    | Lưu trữ dữ liệu gốc (.csv)   |
| **SageMaker**    | Xử lý đặc trưng, train mô hình, lưu model |
| **IAM**          | Quản lý quyền truy cập tài nguyên AWS |

#### **AWS Regions và Availability Zones:**
- **Region**: Geographical area với multiple data centers
- **AZ**: Isolated data center trong một region
- **Workshop region**: **ap-southeast-1 (Singapore)**

### 2. Kiến thức Machine Learning cơ bản

#### Khái niệm chính:
| Thuật ngữ         | Mô tả |
|-------------------|------|
| **Feature**       | Biến đầu vào cho mô hình học máy (ví dụ: tuổi, giới tính, gói cước) |
| **Target / Label**| Biến đầu ra – giá trị cần dự đoán (ví dụ: `churn = Yes/No`) |
| **Feature Engineering** | Quá trình xử lý và biến đổi dữ liệu để mô hình học tốt hơn |
| **Train/Test Split**| Chia dữ liệu thành hai phần: huấn luyện và kiểm thử |
| **Overfitting**   | Mô hình quá khớp với tập train, kém hiệu quả trên dữ liệu mới |
| **XGBoost**       | Thuật toán boosting mạnh, phổ biến cho bài toán phân loại |

## 4. Dữ liệu & Thư mục
### Dataset yêu cầu:
- Customer Churn Dataset (Telco hoặc tương tự)
- Định dạng: .csv
- Có các cột đặc trưng (features) và target (churn)

### Cấu trúc thư mục gợi ý:

```
feature-store-workshop/
├── data/
│   └── churn.csv
├── notebooks/
│   └── 01_prepare_features.ipynb
├── scripts/
│   └── train_model.py
└── README.md
```

## 5. Tài liệu Tham khảo

### **AWS Documentation:**
- [SageMaker Feature Store](https://docs.aws.amazon.com/sagemaker/latest/dg/feature-store.html)
- [SageMaker Python SDK](https://sagemaker.readthedocs.io/en/stable/)
- [AWS Boto3 S3 Docs](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html)
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [XGBoost Overview](https://xgboost.readthedocs.io/en/stable/)


{{% notice tip %}}
Nếu gặp vấn đề trong quá trình chuẩn bị, hãy tham khảo **AWS Support** hoặc **AWS Community Forums** để được hỗ trợ.
Không cần quá giỏi ML – chỉ cần hiểu đúng quy trình và logic xử lý dữ liệu là có thể tham gia workshop này hiệu quả.
{{% /notice %}}