---
title : "Preparation Steps"
date: 2025-06-18
weight : 2
chapter : false
pre : " <b> 2. </b> "
---

## Preparation Overview

To complete this workshop successfully, you need to prepare the necessary prerequisites including your **AWS account**, working environment, and basic technical knowledge. This section will walk you through each preparation step.

## 1. AWS Account

### Account Requirements:
- **AWS Account** fully activated and verified
- **Registered credit card** (used for verification only, not charged within Free Tier)
- **S3 & SageMaker** must be enabled in a supported Region (e.g., `ap-southeast-1`)

### AWS Free Tier Coverage:
This workshop is designed to run entirely within the **AWS Free Tier**:

| Service                  | AWS Free Tier                 | Used in workshop            |
|--------------------------|-------------------------------|-----------------------------|
| **S3**                   | 5GB, 20,000 GET/PUT           | < 500MB, a few hundred requests |
| **SageMaker Notebook**   | 250 hours t3.medium/month     | ~1–2 hours                  |
| **Training Job**         | Not included in Free Tier     | Use small instance + Spot   |
| **Feature Store(Offline)** | Not in Free Tier            | ~10–15 MB of feature data   |

{{% notice info %}}
**Estimated cost**: ~$0 if using Notebook only, < $1–2 USD if Training job and small Feature Store data are included.
{{% /notice %}}

### IAM and Access Setup:

To execute this workshop, your **AWS** account needs the following permissions:

#### **Minimum IAM Permissions:**
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
#### **Recommendations:**
- Use AdministratorAccess in lab or learning environments.
- Avoid using the root account for operations.
- Attach IAM Role to SageMaker notebook instance if using Studio or Classic Notebook.

## 2. Working Environment
### Browser:
- **Modern browser**: Latest Chrome, Firefox, Safari
- **JavaScript enabled**
- **Cookies enabled for AWS Console**
- **Pop-up blocker disabled for AWS domains**

### Network:
- **Stable internet connection** ( ≥ 2 Mbps)
- **Access to AWS endpoints** (not blocked by firewall)
- **HTTPS support** (TLS 1.2+)

### Required Tools:
| Tool    | Minimum Requirement                              |
| ------- | ------------------------------------------------ |
| Python  | >= 3.10                                          |
| pip     | Install packages: `pandas`, `boto3`, `sagemaker` |
| Jupyter | Recommended for Jupyter Notebook                 |
| Git     | To clone project (if needed)                     |
```
pip install pandas boto3 sagemaker
```
AWS CLI (tuỳ chọn):
```
aws configure
```

## 3. Basic Knowledge
### 1. AWS for ML Overview
#### **AWS Services Overview:**
Service	Main Role in Workshop
Amazon S3	Store raw data (.csv)
SageMaker	Feature processing, model training, model storage
IAM	Manage AWS access permissions

#### **AWS Regions and Availability Zones:**
- **Region**: Geographical area with multiple data centers
- **AZ**: Isolated data center within a region
- **Workshop region**: ap-southeast-1 (Singapore)

### 2. Basic Machine Learning Knowledge
#### Key Concepts:
Term	Description
Feature	Input variable for ML models (e.g., age, gender, plan)
Target / Label	Output variable to predict (e.g., churn = Yes/No)
Feature Engineering	Data transformation and preparation for better learning
Train/Test Split	Divide data into training and testing sets
Overfitting	Model fits too closely to training data, performs poorly on new data
XGBoost	A powerful boosting algorithm popular for classification

## 4. Data & Folder Structure
### Required Dataset:
- Customer Churn Dataset (Telco or similar)
- Format: .csv
- Includes both feature columns and target (churn)

### Recommended Folder Structure:
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

## 5. Reference Materials
### **AWS Documentation:**
- [SageMaker Feature Store](https://docs.aws.amazon.com/sagemaker/latest/dg/feature-store.html)
- [SageMaker Python SDK](https://sagemaker.readthedocs.io/en/stable/)
- [AWS Boto3 S3 Docs](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html)
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [XGBoost Overview](https://xgboost.readthedocs.io/en/stable/)

{{% notice tip %}}
If you encounter issues during setup, check AWS Support or AWS Community Forums for help.
You don’t need to be an ML expert — just understanding data pipelines and processing logic is enough to participate effectively in this workshop.
{{% /notice %}}




