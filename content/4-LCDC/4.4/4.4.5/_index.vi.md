---
title : "Mô phỏng cập nhật đơn hàng"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 4.4.5. </b> "
---

Tương tự như trong phòng thí nghiệm trước, hãy thực hiện các cập nhật cấp mục cho bảng **Orders** và quan sát các bản sao cũ của các mục được cập nhật được ghi vào **OrdersHistory**.

Điều hướng đến trang Dịch vụ DynamoDB bằng cách sử dụng AWS Management Console.

Chọn bảng **Orders** sau đó chọn nút **Explore table items** để xem các đơn hàng trên bảng.

Vui lòng tham khảo phần [Simulate Order Updates](https://catalog.workshops.aws/dynamodb-labs/en-US/change-data-capture/ex1/simulate-order-updates) từ phòng thí nghiệm trước nếu bạn cần làm mới cách khám phá các mục bảng bằng AWS Management Console.

Các mục trên bảng sẽ trông quen thuộc từ phòng thí nghiệm trước. Trạng thái của các mục trên bảng **Orders** có thể thay đổi nếu bạn đã thực hiện các mô phỏng bổ sung trong phòng thí nghiệm trước về thu thập dữ liệu thay đổi bằng DynamoDB streams.

![Các mặt hàng trên bảng Orders](/images/4/4.4/10.png)

Cũng khám phá các đơn hàng trên bảng **OrdersHistory**. Số lượng các mục trên bảng sẽ phụ thuộc vào số lượng cập nhật bạn đã thực hiện đối với bảng Orders trong phòng thí nghiệm trước.

Cập nhật trạng thái của đơn hàng ID **9844720** từ **PLACED** sang **COMPLETE** bằng lệnh dưới đây.

```bash
aws dynamodb update-item \
    --table-name Orders \
    --key '{ "id": {"S": "9844720"} }' \
    --update-expression "SET #items = :val1, #status = :val2" \
    --expression-attribute-names '{ "#items": "items", "#status": "status" }' \
    --expression-attribute-values '{ ":val2": { "S": "COMPLETE" },
        ":val1": { "L": [
          { "M": { "id": { "S": "24002126" }, "name": { "S": "Shimmer Wash Eye Shadow" }, "price": { "S": "£13.00" }, "quantity": { "S": "10" }, "status": { "S": "COMPLETE" } } },
          { "M": { "id": { "S": "23607685" }, "name": { "S": "Buffing Grains for Face" }, "price": { "S": "£8.00" }, "quantity": { "S": "11" }, "status": { "S": "COMPLETE" } } }
          ]
        }
      }' \
    --return-values ALL_NEW \
    --return-item-collection-metrics SIZE \
    --return-consumed-capacity TOTAL
```

Kết quả đầu ra sẽ tương tự như dưới đây.

```
{
    "Attributes": {
        "orderDate": {
            "S": "2023-10-01 01:49:13"
        },
        "shipDate": {
            "S": "2023-10-06 13:05:33"
        },
        "status": {
            "S": "COMPLETE"
        },
        "customer": {
            "M": {
                "name": {
                    "S": "Taylor Burnette"
                },
                "id": {
                    "S": "941852721"
                },
                "address": {
                    "S": "31 Walkhampton Avenue, Bradwell Common,MK13 8ND"
                },
                "phone": {
                    "S": "+441663724681"
                }
            }
        },
        "id": {
            "S": "9844720"
        },
        "items": {
            "L": [
                {
                    "M": {
                        "name": {
                            "S": "Shimmer Wash Eye Shadow"
                        },
                        "id": {
                            "S": "24002126"
                        },
                        "quantity": {
                            "S": "10"
                        },
                        "price": {
                            "S": "£13.00"
                        },
                        "status": {
                            "S": "COMPLETE"
                        }
                    }
                },
                {
                    "M": {
                        "name": {
                            "S": "Buffing Grains for Face"
                        },
                        "id": {
                            "S": "23607685"
                        },
                        "quantity": {
                            "S": "11"
                        },
                        "price": {
                            "S": "£8.00"
                        },
                        "status": {
                            "S": "COMPLETE"
                        }
                    }
                }
            ]
        }
    },
    "ConsumedCapacity": {
        "TableName": "Orders",
        "CapacityUnits": 1.0
    }
}
```

Xem các mục trên bảng **Orders** và **OrdersHistory** để xem hiệu ứng của việc cập nhật mục mà bạn đã thực hiện.

Trạng thái của đơn hàng ID **9844720** trên bảng **Orders** sẽ là **COMPLETE** như hình dưới đây.

![Các mặt hàng trên bảng Orders](/images/4/4.4/11.png)

... và sẽ có một bản ghi trên **OrdersHistory** hiển thị trạng thái trước đó của đơn hàng ID **9844720**.

![Các mặt hàng trên bảng OrdersHistory](/images/4/4.4/12.png)

Thực hiện các cập nhật bổ sung cho đơn hàng ID **9953371** trên bảng Orders. Bắt đầu bằng cách thay đổi trạng thái của đơn hàng thành **ACTIVE** sau đó thực hiện một cập nhật khác bằng cách đặt trạng thái của cùng đơn hàng thành **CANCELLED** bằng các lệnh dưới đây.

```bash
aws dynamodb update-item \
    --table-name Orders \
    --key '{ "id": {"S": "9953371"} }' \
    --update-expression "SET #items = :val1, #status = :val2" \
    --expression-attribute-names '{ "#items": "items", "#status": "status" }' \
    --expression-attribute-values '{ ":val1": { "L": [
        { "M": { "id": { "S": "23924636" }, "name": { "S": "Protective Face Lotion" }, "price": { "S": "£3.00" }, "quantity": { "S": "9" }, "status": { "S": "CANCELLED" } } },
        { "M": { "id": { "S": "23514506" }, "name": { "S": "Nail File" }, "price": { "S": "£11.00" }, "quantity": { "S": "13" }, "status": { "S": "PLACED" } } },
        { "M": { "id": { "S": "23508704" }, "name": { "S": "Kitten Heels Powder Finish Foot Creme" }, "price": { "S": "£11.00" }, "quantity": { "S": "10" }, "status": { "S": "PLACED" } } } 
          ]
        },
        ":val2": { "S": "ACTIVE" }
      }' \
    --return-values ALL_NEW \
    --return-item-collection-metrics SIZE \
    --return-consumed-capacity TOTAL
```

Sau đó tiếp tục

```bash
aws dynamodb update-item \
    --table-name Orders \
    --key '{ "id": {"S": "9953371"} }' \
    --update-expression "SET #items = :val1, #status = :val2" \
    --expression-attribute-names '{ "#items": "items", "#status": "status" }' \
    --expression-attribute-values '{ ":val1": { "L": [
        { "M": { "id": { "S": "23924636" }, "name": { "S": "Protective Face Lotion" }, "price": { "S": "£3.00" }, "quantity": { "S": "9" }, "status": { "S": "CANCELLED" } } },
        { "M": { "id": { "S": "23514506" }, "name": { "S": "Nail File" }, "price": { "S": "£11.00" }, "quantity": { "S": "13" }, "status": { "S": "CANCELLED" } } },
        { "M": { "id": { "S": "23508704" }, "name": { "S": "Kitten Heels Powder Finish Foot Creme" }, "price": { "S": "£11.00" }, "quantity": { "S": "10" }, "status": { "S": "CANCELLED" } } }
          ]
        },
        ":val2": { "S": "CANCELLED" }
      }' \
    --return-values ALL_NEW \
    --return-item-collection-metrics SIZE \
    --return-consumed-capacity TOTAL
```

Khám phá các mục trên các bảng **Orders** và **OrdersHistory** để xem kết quả của các cập nhật của bạn. Trạng thái cho đơn hàng ID **9953371** nên được cập nhật trên bảng Orders và nên có hai mục trên bảng OrdersHistory cho đơn hàng ID **9953371**.

![Các mặt hàng trên bảng Orders](/images/4/4.4/13.png)

... và sẽ có hai mục nhập cho đơn hàng ID **9953371** trên bảng **OrdersHistory