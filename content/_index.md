---
title : "Feature Store ML"
date: 2025-07-16
weight : 1 
chapter : false
---

# Building a Reusable Feature Store for ML Model Training with SageMaker

### Overview

- Feature engineering is one of the most time-consuming steps in the Machine Learning model training pipeline. In many organizations, ML teams often reprocess the same features due to the lack of a centralized storage and reuse mechanism. This leads to inconsistency, higher costs, and wasted effort.
- This project proposes a simple yet practical approach to building a Feature Store using Amazon SageMaker Feature Store. The goal is to enable consistent, efficient, and process-compliant feature storage and reuse for model training and retraining. The main use case is Customer Churn Prediction using a public dataset. Key components include: data processing scripts with Pandas, SageMaker Feature Store (offline only), and XGBoost-based training.
- Expected outcomes: increased feature reuse, reduced duplication, faster training, and improved ML pipeline quality.

![Feature-Store-ML](/images/trangchinh.png)
### Contents

 1. [Introduction](1-introduce/)
 2. [Prerequisites](2-Prerequiste/)
 3. [Implementation Steps](3-Implementation_steps/)
 4. [Evaluation and Effectiveness](4-Evaluation_Effectiveness/)
 5. [Resource Cleanup](5-cleanup/)
