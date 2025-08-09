---
title : "Dọn dẹp tài nguyên"
date: 2025-07-16
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

Sau khi hoàn thành workshop, bạn cần dọn dẹp các tài nguyên **AWS** để tránh phát sinh chi phí không mong muốn.

## 1. Xóa S3 Bucket

- Truy cập **AWS Console** → **S3**
- Tìm bucket bạn đã tạo, ví dụ:  
  - `churn-feature-store-demo`
- Thực hiện:
  - Nhấn **Empty** → Xác nhận
  - Sau đó **Delete** → Xác nhận

{{% notice warning %}}
**Bucket S3** phải được làm trống hoàn toàn trước khi có thể xóa.
{{% /notice %}}

## 2. Xóa Feature Group trong SageMaker

- Vào **Amazon SageMaker** → **Feature Store**
- Chọn Feature Group: `churn-features-demo`
- Nhấn **Delete** → Xác nhận xóa

## 3. Xóa kết quả Training và Query trong S3

- Truy cập **S3**
- Xóa thư mục model:
  - `s3://churn-feature-store-demo/model/`
- (Tùy chọn) Xóa thư mục lưu kết quả truy vấn Athena:
  - `s3://churn-feature-store-demo/query/`

## 4. Xóa SageMaker Notebook Instance (nếu có tạo)

- Truy cập **SageMaker** → **Notebook Instances**
- Chọn Notebook đã tạo
- Nhấn **Stop** trước → sau đó **Delete**
- Xác nhận xóa

## 5. Xóa IAM Role (Tùy chọn)

- Truy cập **IAM** → **Roles**
- Tìm các Role được SageMaker tạo:
  - Ví dụ: `AmazonSageMaker-ExecutionRole-xxxxxx`
- Chỉ nên xóa nếu không sử dụng cho mục đích khác

{{% notice tip %}}
Bạn có thể giữ lại **IAM Role** nếu có kế hoạch sử dụng lại trong tương lai.
{{% /notice %}}

## 6. Kiểm tra Billing

- Truy cập **Billing Dashboard** → **Cost Explorer**
- Lọc theo các dịch vụ: **S3**, **SageMaker**, **Athena**, v.v.
- Đảm bảo không còn tài nguyên nào phát sinh chi phí
- Kiểm tra **Free Tier Usage** nếu dùng tài khoản mới

## 7. Checklist hoàn tất ✅

- [ ] Đã xóa **S3 Bucket**  
- [ ] Đã xóa **Feature Group**  
- [ ] Đã xóa kết quả **Training/Query**  
- [ ] Đã xóa **Notebook Instance** (nếu có)  
- [ ] Đã xóa **IAM Roles** (nếu cần)  
- [ ] Đã kiểm tra **Billing** và không phát sinh chi phí  

{{% notice info %}}
Một số tài nguyên có thể mất vài phút để biến mất khỏi AWS Console sau khi xóa.
{{% /notice %}}
