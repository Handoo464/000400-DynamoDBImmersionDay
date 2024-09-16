---
title : "Mô phỏng cập nhật đơn hàng"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 4.3.5. </b> "
---

Sau khi tạo bảng Orders, bạn đã tải lên một số đơn hàng mẫu vào bảng. Hãy khám phá các mặt hàng trên bảng Orders trước khi mô phỏng bất kỳ cập nhật nào đối với các đơn hàng trên bảng.

1. Điều hướng đến trang Dịch vụ DynamoDB bằng cách sử dụng AWS Management Console.

2. Chọn bảng **Orders**, sau đó chọn nút **Explore table items** để xem các đơn hàng trên bảng.

![Các mặt hàng trên bảng Orders](/images/4/4.3/14.png)

![Các mặt hàng trên bảng Orders](/images/4/4.3/15.png)

Sẽ có 4 đơn hàng trên bảng, tất cả đều có trạng thái là **PLACED** như hình dưới đây.

![Các mặt hàng trên bảng Orders](/images/4/4.3/16.png)

Bạn có thể khám phá nội dung của từng đơn hàng bằng cách chọn **id** của mục bằng Console DynamoDB.

Sẽ không có dữ liệu nào trên bảng **OrdersHistory** tại thời điểm này vì chưa có dữ liệu nào được ghi vào đó.

Bạn có thể mô phỏng cập nhật các mục trên bảng Orders bằng cách sử dụng AWS Management Console hoặc AWS CLI. Mở rộng phần thích hợp dưới đây để có hướng dẫn về cách tiến hành với phương pháp ưa thích của bạn.

{{%notice info%}}
Mô phỏng cập nhật sử dụng AWS Management Console

1. Chọn bảng **Orders**.
![AWS Lambda function console](/images/4/4.3/21.png)
2. Chọn nút **Explore table items**.
![AWS Lambda function console](/images/4/4.3/22.png)
3. Nhấp vào ID đơn hàng **6421680** trên bảng.
![AWS Lambda function console](/images/4/4.3/23.png)
4. Thay đổi trạng thái của đơn hàng từ `PLACED` sang `COMPLETE`.
5. Chọn nút **Save and close**.
![AWS Lambda function console](/images/4/4.3/24.png)
{{%/notice%}}

{{%notice info%}}
Mô phỏng cập nhật sử dụng AWS CLI\
Áp dụng cập nhật cho ID đơn hàng **642168**
```bash
aws dynamodb update-item \
    --table-name Orders \
    --key '{ "id": {"S": "6421680"} }' \
    --update-expression "SET #items = :val1, #status = :val2" \
    --expression-attribute-names '{ "#items": "items", "#status": "status" }' \
    --expression-attribute-values '{ ":val2": { "S": "COMPLETE" },
        ":val1": { "L": [
            { "M": { "id": { "S": "23769901"}, "name": { "S": "Hydrating Face Cream" }, "price": { "S": "£12.00" }, "quantity": { "S": "8"}, "status": { "S": " COMPLETE" } } },
            { "M": { "id": { "S": "23673445" }, "name": { "S": "EXTRA Repair Serum" }, "price": { "S": "£10.00" }, "quantity": { "S": "5" }, "status": { "S": " COMPLETE" } } }
          ]
        } 
      }' \
    --return-values ALL_NEW \
    --return-item-collection-metrics SIZE \
    --return-consumed-capacity TOTAL
```
Kết quả sẽ tương tự như sau.
```
{
    "Attributes": {
        "orderDate": {
            "S": "2023-10-01 20:39:08"
        },
        "shipDate": {
            "S": "2023-10-04 16:29:36"
        },
        "status": {
            "S": "COMPLETE"
        },
        "customer": {
            "M": {
                "name": {
                    "S": "Brody Dent"
                },
                "id": {
                    "S": "558490551"
                },
                "address": {
                    "S": "3 Bailey Lane, Clenchwarton,PE34 4AY"
                },
                "phone": {
                    "S": "+441268381612"
                }
            }
        },
        "id": {
            "S": "6421680"
        },
        "items": {
            "L": [
                {
                    "M": {
                        "name": {
                            "S": "Hydrating Face Cream"
                        },
                        "id": {
                            "S": "23769901"
                        },
                        "quantity": {
                            "S": "8"
                        },
                        "price": {
                            "S": "£12.00"
                        },
                        "status": {
                            "S": " COMPLETE"
                        }
                    }
                },
                {
                    "M": {
                        "name": {
                            "S": "EXTRA Repair Serum"
                        },
                        "id": {
                            "S": "23673445"
                        },
                        "quantity": {
                            "S": "5"
                        },
                        "price": {
                            "S": "£10.00"
                        },
                        "status": {
                            "S": " COMPLETE"
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
{{%/notice%}}

Bây giờ hãy khám phá các bảng **Orders** và **OrdersHistory** để xem các kết quả cập nhật mục mà bạn đã thực hiện.

Trạng thái của đơn hàng ID 6421680 trên bảng **Orders** sẽ là **COMPLETE** như hình dưới đây.

![Các mặt hàng trên bảng Orders](/images/4/4.3/17.png)

... và sẽ có một bản ghi trên **OrdersHistory** hiển thị trạng thái trước đó của đơn hàng ID 6421680.

![Các mặt hàng trên bảng OrdersHistory](/images/4/4.3/18.png)

Thực hiện các cập nhật bổ sung cho đơn hàng ID **4514280** trên bảng Orders. Lần này, đầu tiên thay đổi trạng thái của đơn hàng thành **COMPLETE**, sau đó thay đổi trạng thái của một số mặt hàng trên cùng đơn hàng thành **RETURNED** bằng cách sử dụng các lệnh dưới đây.

```bash
aws dynamodb update-item \
    --table-name Orders \
    --key '{ "id": {"S": "4514280"} }' \
    --update-expression "SET #items = :val1, #status = :val2" \
    --expression-attribute-names '{ "#items": "items", "#status": "status" }' \
    --expression-attribute-values '{ ":val1": { "L": [ 
        { "M": { "id": { "S": "23884750" }, "name": { "S": "Metallic Long-Wear Cream Shadow" }, "price": { "S": "£15.00" }, "quantity": { "S": "13" }, "status": { "S": "COMPLETE" } } },
        { "M": { "id": { "S": "23699354" }, "name": { "S": "Eye Liner" }, "price": { "S": "£9.00" }, "quantity": { "S": "8" }, "status": { "S": "COMPLETE" } } },
        { "M": { "id": { "S": "23599030" }, "name": { "S": "Bronzing Powder" }, "price": { "S": "£12.00" }, "quantity": { "S": "10" }, "status": { "S": "COMPLETE" } } }
          ] },
        ":val2": { "S": "COMPLETE" }
      }' \
    --return-values ALL_NEW \
    --return-item-collection-metrics SIZE \
    --return-consumed-capacity TOTAL
```

Sau đó tiếp tục

```bash
aws dynamodb update-item \
    --table-name Orders \
    --key '{ "id": {"S": "4514280"} }' \
    --update-expression "SET #items = :val1, #status = :val2" \
    --expression-attribute-names '{ "#items": "items", "#status": "status" }' \
    --expression-attribute-values '{ ":val1": { "L": [ 
        { "M": { "id": { "S": "23884750" }, "name": { "S": "Metallic Long-Wear Cream Shadow" }, "price": { "S": "£15.00" }, "quantity": { "S": "13" }, "status": { "S": "COMPLETE" } } },
        { "M": { "id": { "S

": "23699354" }, "name": { "S": "Eye Liner" }, "price": { "S": "£9.00" }, "quantity": { "S": "8" }, "status": { "S": "RETURNED" } } },
        { "M": { "id": { "S": "23599030" }, "name": { "S": "Bronzing Powder" }, "price": { "S": "£12.00" }, "quantity": { "S": "10" }, "status": { "S": "RETURNED" } } } ] },
        ":val2": { "S": "COMPLETE" }
      }' \
    --return-values ALL_NEW \
    --return-item-collection-metrics SIZE \
    --return-consumed-capacity TOTAL
```

Khám phá các mặt hàng trên các bảng Orders và OrdersHistory để xem kết quả của các cập nhật của bạn.

Trạng thái của đơn hàng ID 4514280 trên bảng Orders sẽ là **COMPLETE** như hình dưới đây.

![Các mặt hàng trên bảng Orders](/images/4/4.3/19.png)

... và sẽ có hai mục nhập cho đơn hàng ID 4514280 trên bảng OrdersHistory hiển thị các trạng thái trước đó của đơn hàng.

![Các mặt hàng trên bảng OrdersHistory](/images/4/4.3/20.png)

{{%notice tip%}}
**Lưu ý:** Thứ tự cập nhật trên bảng Orders được duy trì bởi các luồng DynamoDB khi các thay đổi được gửi đến hàm lambda tạo lịch sử đơn hàng. Vì các mục trên bảng OrdersHistory có khóa sắp xếp - sk, là một dấu thời gian, các mục trên bảng OrderHistory có thể được sắp xếp theo thứ tự chúng được tạo ra.
{{%/notice%}}