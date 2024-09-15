---
title : "Simulate Order Updates"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 4.5. </b> "
---

After creating the Orders table, you uploaded some sample orders to the table. Lets explore the items on the Orders table before simulating any updates to any orders on the table.

1. Navigate to the DynamoDB Service page using the AWS Management Console.

2. Select the **Orders** table then select the **Explore table items** button to view the orders on the table.

![Orders Table Items](/images/4/4.3/14.png)

![Orders Table Items](/images/4/4.3/15.png)

There will be 4 orders on the table all having a status of **PLACED** as shown in the image below.

![Orders Table Items](/images/4/4.3/16.png)

Feel free to explore the content of each order by selecting the **id** of the item using the DynamoDB Console.

There will be no data on the **OrdersHistory** table at this point beacuse no data has been written to it.

You can simulate updates to items on Orders table by using the AWS Management Console or by using the AWS CLI. Expand the appropriate section below for instructions on how to proceed with your preferred method.

{{%notice info%}}
Simulate update using the AWS Management Console

1. Select the **Orders** table.
![AWS Lambda function console](/images/4/4.3/21.png)
2. Select the **Explore table items** button.
![AWS Lambda function console](/images/4/4.3/22.png)
3. Click on order ID **6421680** on the table.
![AWS Lambda function console](/images/4/4.3/23.png)
4. Change the status of the order from `PLACED` to `COMPLETE`.
5. Selectthe **Save and close** button.\
![AWS Lambda function console](/images/4/4.3/24.png)
{{%/notice%}}
{{%notice info%}}
Simulate update using the AWS CLI\
Apply an update to order ID **642168**
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
The output should be similar to the one below.
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


Now explore the **Orders** and **OrdersHistory** tables to see the effects item update you performed.

The status of order ID 6421680 on the **Orders** table should be **COMPLETE** as shown in the image below.

![Orders Table Items](/images/4/4.3/17.png)

... and there should be a single record on the **OrdersHistory** showing the previous state of order ID 6421680.

![OrdersHistory Table Items](/images/4/4.3/18.png)

Perform additional updates for order ID **4514280** on the Orders table. This time first change the status of the order to **COMPLETE** then alter the status of some items on the same order to **RETURNED** using the commands below.

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

Followed by

```bash
aws dynamodb update-item \
    --table-name Orders \
    --key '{ "id": {"S": "4514280"} }' \
    --update-expression "SET #items = :val1, #status = :val2" \
    --expression-attribute-names '{ "#items": "items", "#status": "status" }' \
    --expression-attribute-values '{ ":val1": { "L": [ 
        { "M": { "id": { "S": "23884750" }, "name": { "S": "Metallic Long-Wear Cream Shadow" }, "price": { "S": "£15.00" }, "quantity": { "S": "13" }, "status": { "S": "COMPLETE" } } },
        { "M": { "id": { "S": "23699354" }, "name": { "S": "Eye Liner" }, "price": { "S": "£9.00" }, "quantity": { "S": "8" }, "status": { "S": "RETURNED" } } },
        { "M": { "id": { "S": "23599030" }, "name": { "S": "Bronzing Powder" }, "price": { "S": "£12.00" }, "quantity": { "S": "10" }, "status": { "S": "RETURNED" } } } ] },
        ":val2": { "S": "COMPLETE" }
      }' \
    --return-values ALL_NEW \
    --return-item-collection-metrics SIZE \
    --return-consumed-capacity TOTAL
```

Explore the items on the Orders and OrdersHistory tables to see the result of your updates.

The status of order ID 4514280 on the Orders table should be **COMPLETE** as shown in the image below.

![Orders Table Items](/images/4/4.3/19.png)

... and there should be two entries for order ID 4514280 on the OrdersHistory table showing the previous states of the order.

![OrdersHistory Table Items](/images/4/4.3/20.png)

{{%notice tip%}}
**Note:** The order of updates to the Orders table is preserved by DynamoDB streams when changes are sent to the create order history lambda function. Since items on the OrdersHistory table have a sort key - sk, that is a timestamp, items on the OrderHistory table can be sorted in the order that they were created.
{{%/notice%}}
