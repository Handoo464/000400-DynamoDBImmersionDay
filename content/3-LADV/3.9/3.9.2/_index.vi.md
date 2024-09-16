---
title : "Bước 2 - Xem lại chính sách AWS IAM cho vai trò IAM"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.9.2. </b> "
---
Chúng tôi đã tạo trước vai trò IAM sẽ được sử dụng làm `DDBReplicationRole`[Vai trò thực thi AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) . Vai trò IAM này cho phép cung cấp một số quyền đối với hàm AWS Lambda mà chúng tôi sẽ cần để sao chép dữ liệu.

Xem lại chính sách sau đây được đính kèm với vai trò IAM.`DDBReplicationRole`

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dynamodb:DescribeStream",
                "dynamodb:GetRecords",
                "dynamodb:GetShardIterator",
                "dynamodb:ListStreams"
            ],
            "Resource": [
                "*"
            ],
            "Effect": "Allow"
        },
        {
            "Action": [
                "dynamodb:DeleteItem",
                "dynamodb:PutItem"
            ],
            "Resource": [
                "*"
            ],
            "Effect": "Allow"
        },
        {
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": [
                "*"
            ],
            "Effect": "Allow"
        }
    ]
}
```

Đây là một số quyền được cấp cho hàm Lambda trong chính sách:

- Dịch vụ AWS Lambda phải có khả năng gọi DynamoDB Streams và truy xuất bản ghi từ luồng.

```json
{
    "Action": [
        "dynamodb:DescribeStream",
        "dynamodb:GetRecords",
        "dynamodb:GetShardIterator",
        "dynamodb:ListStreams"
    ],
    "Resource": [
        "*"
    ],
    "Effect": "Allow"
}
```

- Hàm Lambda có thể đặt và xóa các mục trong bất kỳ bảng DynamoDB nào.

```json
{
    "Action": [
        "dynamodb:DeleteItem",
        "dynamodb:PutItem"
    ],
    "Resource": [
        "*"
    ],
    "Effect": "Allow"
}
```

- Nhật ký sự kiện được phát hành lên Amazon CloudWatch Logs (nhưng trong phòng thực hành này chúng không khả dụng).

```json
{
    "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
    ],
    "Resource": [
        "*"
    ],
    "Effect": "Allow"
}
```