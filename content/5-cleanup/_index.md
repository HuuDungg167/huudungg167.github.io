---
title : "Cleanup Resources"
date: 2025-07-16
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

After completing the workshop, you should clean up your **AWS resources** to avoid incurring unexpected costs.

## 1. Delete S3 Buckets

- Go to **AWS Console** → **S3**
- Locate your workshop buckets:
  - `churn-feature-store-demo`
- For each bucket:
  - Click **Empty** → Confirm
  - Then **Delete** → Confirm

{{% notice warning %}}
S3 buckets must be emptied before they can be deleted.
{{% /notice %}}

## 2. Delete SageMaker Feature Group

- Go to **Amazon SageMaker** → **Feature Store**
- Find `churn-features-demo`
- Select and **Delete Feature Group**
- Confirm deletion

## 3. Delete SageMaker Training Job Output

- Go to **S3**
- Delete the model artifacts folder:
  - `s3://churn-feature-store-demo/model/`
- Optional: Delete Athena query results in:
  - `s3://churn-feature-store-demo/query/`

## 4. Delete SageMaker Notebook Instance (if created)

- Go to **Amazon SageMaker** → **Notebook instances**
- Select your instance
- **Stop** it first, then choose **Delete**
- Confirm deletion

## 5. Delete IAM Roles (Optional)

- Go to **IAM** → **Roles**
- Look for roles automatically created by SageMaker:
  - Example: `AmazonSageMaker-ExecutionRole-xxxxxxx`
- Only delete roles if they are not reused elsewhere

{{% notice tip %}}
IAM roles created for notebooks or training jobs can be retained if you plan to reuse them.
{{% /notice %}}

## 6. Check Billing

- Open **Billing Dashboard** → **Cost Explorer**
- Filter by services: **S3**, **SageMaker**, **Athena**, etc.
- Ensure no active resources are generating charges
- Check **Free Tier Usage** if you're using a new account

## 7. Final Checklist ✅

- [ ] **S3 Buckets** deleted  
- [ ] **Feature Group** deleted  
- [ ] **Training Artifacts** deleted  
- [ ] **Notebook Instance** deleted (if any)  
- [ ] **IAM Roles** deleted (if needed)  
- [ ] **Billing** reviewed, no extra charges  

{{% notice info %}}
Some resources may take a few minutes to disappear from the AWS Console after deletion.
{{% /notice %}}
