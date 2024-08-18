---
title : "Hạn chế xóa sao lưu"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 1.4.5. </b> "
---

Khách hàng thường yêu cầu rằng các nhà phát triển/quản trị viên của họ nên được phép tạo và xóa các bảng DynamoDB nhưng không nên được phép xóa các bản sao lưu.

Bạn có thể đạt được điều này bằng cách tạo chính sách IAM. Dưới đây là một ví dụ về chính sách IAM của AWS cho phép "Tạo Bảng", "Liệt kê Bảng", "Tạo Sao Lưu" và "Xóa Bảng" và từ chối "Xóa Sao Lưu" của bảng DynamoDB.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "dynamodb:CreateTable",
                "dynamodb:CreateBackup",
                "dynamodb:DeleteTable"
            ],
            "Resource": "arn:aws:dynamodb:us-east-1:123456789:table/*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "dynamodb:ListTables",
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor2",
            "Effect": "Deny",
            "Action": "dynamodb:DeleteBackup",
            "Resource": "arn:aws:dynamodb:us-east-1:123456789:table/*/backup/*"
        }
    ]
}


```

[Tại trang tài liệu này, bạn sẽ tìm thấy thêm thông tin về việc sử dụng IAM với các bản sao lưu DynamoDB.](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/backuprestore_IAM.html) 

Bạn cũng có thể hạn chế trong AWS Backup bằng cách từ chối, chẳng hạn như từ chối "DeleteBackupSelection" trong chính sách IAM.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "backup:DeleteBackupSelection",
                "backup:CreateBackupSelection",
                "backup:StartBackupJob",
                "backup:CreateBackupPlan",
                "backup:ListBackupSelections",
                "backup:ListRecoveryPointsByBackupVault",
                "backup:GetBackupVaultAccessPolicy",
                "backup:GetBackupSelection"
            ],
            "Resource": [
                "arn:aws:backup:us-east-1:123456789:backup-plan:*",
                "arn:aws:backup:us-east-1:123456789:backup-vault:*"
            ]
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Deny",
            "Action": "backup:DeleteBackupSelection",
            "Resource": "arn:aws:backup:us-east-1:123456789:backup-plan:*"
        }
    ]
}


```

Bạn có thể áp dụng chính sách này cho vai trò và gán vai trò cho nhóm IAM. 
Bây giờ, người dùng thuộc nhóm IAM này sẽ thừa hưởng quyền.

Giả sử bây giờ người dùng cố gắng xóa bản sao lưu trong AWS Backup.

![Restrict Backup Deletion 1](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/hands-on-labs/backup/restrict_delete_1.png)

Người dùng nhận được thông báo lỗi truy cập bị từ chối do thiếu quyền để xóa bản sao lưu.

![Restrict Backup Deletion 2](https://static.us-east-1.prod.workshops.aws/public/c768eb2c-360b-491e-8422-bfd253e11581/static/images/hands-on-labs/backup/restrict_delete_2.png)
