---
title : "Các bước thực hiện"
date: 2025-07-16
weight : 3
chapter : false
pre : " <b> 3. </b> "
---

## 🛠️ Các bước thực hiện

Phần này hướng dẫn từng bước cụ thể để triển khai hệ thống huấn luyện mô hình Machine Learning có khả năng tái sử dụng đặc trưng, sử dụng **Amazon SageMaker Feature Store**.
Chúng ta sẽ sử dụng mô hình **XGBoost** để huấn luyện bài toán **Customer Churn Prediction** từ tập dữ liệu dạng `.csv`.

## 1. Upload dữ liệu lên Amazon S3
🌟 Mục tiêu:
Đưa tập dữ liệu CSV gốc lên S3 để sử dụng trong quá trình xử lý.

📂 Thực hiện:
- Truy cập AWS Console > S3.
- Tạo một bucket mới, ví dụ: churn-feature-store-demo.
- Upload tập churn.csv lên thư mục raw/
Hoặc sử dụng:
```
aws s3 mb s3://churn-feature-store-demo
aws s3 cp churn.csv s3://churn-feature-store-demo/raw/
```

💬 Lưu ý:
Bucket nên cùng Region với SageMaker (ví dụ ap-southeast-1)
Dữ liệu gốc không nên chứa thông tin nhạy cảm

## 2. Xử lý đặc trưng bằng Pandas
🌟 Mục tiêu:
Tiền xử lý dữ liệu: encode, scale, và chuẩn hóa các đặc trưng.
```
import pandas as pd
import boto3
from io import StringIO

s3 = boto3.client('s3')
obj = s3.get_object(Bucket='churn-feature-store-demo', Key='raw/churn.csv')
df = pd.read_csv(obj['Body'])

# Encode target
df["churn_flag"] = df["churn"].map({"Yes": 1, "No": 0})

# Add required fields
from datetime import datetime

df["record_id"] = df.index.astype(str)
df["event_time"] = datetime.now().isoformat()
```
💬 Lưu ý:
- Nên kiểm tra thống kê dữ liệu: nulls, dtype, mean, std...
- Tránh dùng label (churn) để tính các đặc trưng → tránh feature leakage


## 3. Tạo FeatureGroup trong SageMaker
🌟 Mục tiêu:
Khai báo schema cho đặc trưng và tạo một FeatureGroup.
- 🔄 Các bước:
```
from sagemaker.feature_store.feature_group import FeatureGroup
from sagemaker.feature_store.feature_definition import FeatureDefinition, FeatureTypeEnum
from sagemaker.session import Session

feature_group = FeatureGroup(name="churn-features-demo", sagemaker_session=Session())

feature_definitions = [
    FeatureDefinition("record_id", FeatureTypeEnum.STRING),
    FeatureDefinition("event_time", FeatureTypeEnum.STRING),
    FeatureDefinition("monthly_charges", FeatureTypeEnum.FRACTIONAL),
    FeatureDefinition("tenure", FeatureTypeEnum.INTEGRAL),
    FeatureDefinition("churn_flag", FeatureTypeEnum.INTEGRAL),
]
```
💬 Lưu ý:
- record_id phải là duy nhất.
- event_time bắt buộc trong offline store.

## 4. Ingest dữ liệu vào Feature Store
🌟 Mục tiêu:
Đưa dữ liệu vào Feature Store theo schema đã khai báo.
- 🔄 Các bước:
```
from sagemaker.feature_store.inputs import FeatureValue
from sagemaker.feature_store.feature_group import OfflineStoreConfig, S3StorageConfig

feature_group.create(
    feature_definitions=feature_definitions,
    record_identifier_name="record_id",
    event_time_feature_name="event_time",
    offline_store_config=OfflineStoreConfig(
        s3_storage_config=S3StorageConfig(s3_uri="s3://churn-feature-store-demo/feature-store/")
    ),
    role_arn=sagemaker.get_execution_role()
)

feature_group.ingest(data_frame=df, max_workers=3, wait=True)
```

## 5. Truy vấn đặc trưng để huấn luyện
🌟 Mục tiêu:
Truy xuất đặc trưng đã lưu để dùng trong training.
- 🔄 Các bước:
```
query = feature_group.athena_query()
query.run(
    query_string=f"SELECT * FROM {feature_group.name}",
    output_location="s3://churn-feature-store-demo/query/"
)
query.wait()
dataset = pd.read_csv(query.output_location + "query.csv")
```

## 6. Huấn luyện mô hình XGBoost
🌟 Mục tiêu:
Huấn luyện mô hình phân loại bằng SageMaker XGBoost.
- 🔄 Các bước:
```
from sagemaker.inputs import TrainingInput
from sagemaker.xgboost.estimator import XGBoost

xgb = XGBoost(
    entry_point="train_model.py",
    framework_version="1.3-1",
    role=sagemaker.get_execution_role(),
    instance_count=1,
    instance_type="ml.m5.large",
    output_path="s3://churn-feature-store-demo/model/",
    hyperparameters={"objective": "binary:logistic", "num_round": 50}
)

xgb.fit({"train": TrainingInput("s3://churn-feature-store-demo/query/query.csv")})
```

## 7. Xuất mô hình và kết thúc pipeline
💾 Model artifact:
Sau khi training, model được lưu tại:
```
s3://churn-feature-store-demo/model/<training-job-id>/output/model.tar.gz
```
Bạn có thể tải về, deploy API hoặc sử dụng lại sau này.

## 8. Cleanup (tuỳ chọn)
### Xoá FeatureGroup
```
feature_group.delete()
```
### Xoá bucket hoặc tài nguyên khác
```
aws s3 rm s3://churn-feature-store-demo --recursive
```