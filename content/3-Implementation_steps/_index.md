---
title : "Execution Steps"
date: 2025-07-16
weight : 3
chapter : false
pre : " <b> 3. </b> "
---

## 🛠️ Execution Steps

This section provides step-by-step guidance to implement a Machine Learning training system that supports reusable features using **Amazon SageMaker Feature Store**.  
We will use the **XGBoost** model to train a **Customer Churn Prediction** problem from a `.csv` dataset.

## 1. Upload Data to Amazon S3
🌟 Objective:  
Upload the original CSV dataset to S3 for processing.

📂 Instructions:
- Go to AWS Console > S3.
- Create a new bucket, e.g., churn-feature-store-demo.
- Upload the churn.csv file to the `raw/` folder.  
Or use:
```
aws s3 mb s3://churn-feature-store-demo
aws s3 cp churn.csv s3://churn-feature-store-demo/raw/
```

💬 Note:  
The bucket should be in the same Region as SageMaker (e.g., ap-southeast-1)  
Original data should not contain sensitive information.

## 2. Feature Engineering with Pandas
🌟 Objective:  
Preprocess data: encode, scale, and normalize features.
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
💬 Note:
- Check data statistics: nulls, dtype, mean, std...
- Avoid using the label (churn) in feature calculation → prevent feature leakage

## 3. Create FeatureGroup in SageMaker
🌟 Objective:  
Define the schema for features and create a FeatureGroup.  
- 🔄 Steps:
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
💬 Note:
- `record_id` must be unique.
- `event_time` is required for the offline store.

## 4. Ingest Data into Feature Store
🌟 Objective:  
Push data into the Feature Store based on the defined schema.  
- 🔄 Steps:
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

## 5. Query Features for Training
🌟 Objective:  
Retrieve saved features to use in model training.
- 🔄 Steps:
```
query = feature_group.athena_query()
query.run(
    query_string=f"SELECT * FROM {feature_group.name}",
    output_location="s3://churn-feature-store-demo/query/"
)
query.wait()
dataset = pd.read_csv(query.output_location + "query.csv")
```


## 6. Train XGBoost Model
🌟 Objective:  
Train a classification model using SageMaker XGBoost.
- 🔄 Steps:
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

## 7. Export Model and Finalize Pipeline
💾 Model artifact:
- After training, the model is saved at:
```
s3://churn-feature-store-demo/model/<training-job-id>/output/model.tar.gz
```
- You can download, deploy as an API, or reuse it later.

## 8. Cleanup (optional)
### Delete FeatureGroup
```
feature_group.delete()
```
### Delete bucket or other resources
```
aws s3 rm s3://churn-feature-store-demo --recursive
```






