---
title: "Introduction"
date: 2025-07-16
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

## Workshop Overview

This workshop guides you through building a **reusable Feature Store** for training machine learning models using **Amazon SageMaker Feature Store** (Offline mode). You will learn how to:
- Load data from S3
- Perform feature engineering using Pandas
- Register and store features in the Feature Store
- Train an XGBoost model using stored features

## Why Use a Feature Store?

**Feature engineering** is one of the most time-consuming and repetitive steps in an ML training pipeline. Without a shared feature repository, teams often face:
- Redundant processing
- Schema mismatches and data leakage issues
- High maintenance and testing effort
- Delays in deployment due to inconsistencies

### Key Benefits of a Feature Store:
- **Reusable features** across pipelines and teams
- **Accelerated model training** through schema standardization
- **Reduced duplication** and maintenance burden
- **Improved traceability** and ML pipeline auditability

## System Architecture

### Feature Processing Flow:
CSV → S3 → Pandas Script → Feature Store (Offline Only)  
→ SageMaker Training (XGBoost) → Model Artifact
![Picture1](/images/Sodo.png)

### Key AWS Services:

#### 1. **Amazon S3**
- Stores raw CSV datasets
- Integrated directly with Pandas for reading

#### 2. **Pandas + Python**
- Reads data from S3
- Performs transformations:
  - Data cleaning (null handling, filtering)
  - Encoding (LabelEncoding, OneHot)
  - Feature scaling (MinMax/Standard)
- Writes processed features into the Feature Store via SDK

#### 3. **SageMaker Feature Store (Offline Mode)**
- Time-aware feature storage (`event_time`)
- Organized into `FeatureGroups` with predefined schemas
- Stores data in Parquet format on S3
- Enables feature retrieval for training and evaluation

##### Advantages:
- **Decouples feature storage from processing code**
- **Supports sharing across ML pipelines**
- **Reusable – eliminates repeated processing**

#### 4. **SageMaker Training Job**
- Queries features directly from the Feature Store
- Trains an XGBoost model
- Saves the trained model to S3 (`.tar.gz` format)
- Supports further evaluation or deployment

## Final Objectives

This workshop will help you:

- Reuse features across multiple training jobs
- Build consistent and maintainable training pipelines
- Apply concepts to real-world ML projects
- Prepare for integration with SageMaker Pipelines

## Workshop Prerequisites

Before you start, make sure you have:

- **AWS Account** with permission to create the following:
  - Amazon S3
  - Amazon SageMaker
  - IAM Roles
- If using a new account: **enable Free Tier access**
- **Minimum required IAM permissions**:
  - `AmazonS3FullAccess` (or scoped to specific bucket)
  - `AmazonSageMakerFullAccess`
  - `IAMPassRole` (if creating roles for notebook/training)

{{% notice info %}}
This workshop is designed to run within the **AWS Free Tier**. Total estimated cost is less than **$1 USD** if you follow the instructions correctly.
{{% /notice %}}
