---
title : "Tổng quan kịch bản"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 4.2. </b> "
---

Hãy tưởng tượng bạn có một trang web thương mại điện tử nơi khách hàng đặt hàng cho các mặt hàng khác nhau. Trang web này dựa vào Amazon DynamoDB và yêu cầu ghi lại tất cả các sự kiện từ khi một đơn hàng được đặt cho đến khi mặt hàng được giao.

Yêu cầu của trang web:

- Trạng thái của các đơn hàng đặt trên trang web của bạn có thể là `ACTIVE`, `PLACED`, `COMPLETE`, hoặc `CANCELLED`.
- Bạn cần giữ bản hiện tại của các đơn hàng của khách hàng trên bảng cơ sở dữ liệu chính được sử dụng bởi ứng dụng của bạn.
- Mỗi đơn hàng có thuộc tính `status` và chứa danh sách một hoặc nhiều mặt hàng.

Dưới định dạng JSON, một mục trong bảng đơn hàng có các thuộc tính sau:

```
{
    "id": "string",
    "status": "string",
    "customer": {
        "id": "string",
        "address": "string",
        "name": "string",
        "phone": "string"
    },
    "orderDate": "YYYY-MM-DD hh:mm:ss",
    "shipDate": "YYYY-MM-DD hh:mm:ss",
    "items": [
        {
            "id": "string",
            "name": "string",
            "price": "string",
            "quantity": "string",
            "status": "string"
        }
    ]
}
```

Bạn sẽ triển khai một giải pháp để đáp ứng yêu cầu này bằng cách sử dụng hai bảng DynamoDB - Orders và OrdersHistory; và một giải pháp streaming để sao chép các thay đổi cấp mục từ bảng Orders sang bảng OrdersHistory.
