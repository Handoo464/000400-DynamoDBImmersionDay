---
title : "Hạn chế xóa sao lưu"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 1.4.5. </b> "
---

Khách hàng thường yêu cầu cho phép nhà phát triển/quản trị viên của họ tạo và xóa bảng DynamoDB, nhưng không được phép xóa các bản sao lưu.

Bạn có thể đạt được điều này bằng cách tạo chính sách IAM. Dưới đây là ví dụ về chính sách AWS IAM cho phép “Tạo Bảng” (Create Table), “Liệt kê Bảng” (List Table), “Tạo Sao lưu” (Create Backup) và “Xóa Bảng” (Delete Table), nhưng từ chối quyền “Xóa Sao lưu” (Delete Backup) của bảng DynamoDB.

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

[Tại trang tài liệu này, bạn sẽ tìm thấy thêm thông tin về việc sử dụng IAM với sao lưu DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/backuprestore_IAM.html).

Bạn cũng có thể giới hạn quyền trong AWS Backup bằng cách từ chối quyền “DeleteBackupSelection” trong chính sách IAM.

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

Bạn có thể áp dụng chính sách này cho một vai trò (role) và gán vai trò đó cho một nhóm IAM. Bây giờ, người dùng thuộc nhóm IAM này sẽ thừa hưởng các quyền hạn này.

Giả sử người dùng cố gắng xóa bản sao lưu trong AWS Backup.

![Hạn chế xóa sao lưu 1](/images/1/1.4/1.4.5/1.png)

Người dùng sẽ gặp lỗi "Access Denied" do không có đủ quyền để xóa bản sao lưu.

![Hạn chế xóa sao lưu 2](/images/1/1.4/1.4.5/2.png)