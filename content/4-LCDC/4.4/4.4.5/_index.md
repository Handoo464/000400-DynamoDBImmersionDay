---
title : "Simulate Order Updates"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 4.4.5. </b> "
---
Similar to the previous lab, lets perform item level updates to the **Orders** table and watch old copies of updated items get written to the **OrdersHistory**.

Navigate to the DynamoDB Service page using the AWS Management Console.

Select the **Orders** table then select **Explore table items** button to view the orders on the table.

Please refer to the [Simulate Order Updates](https://catalog.workshops.aws/dynamodb-labs/en-US/change-data-capture/ex1/simulate-order-updates) section from the previous lab if you need a refresher on exploring table items using the AWS Management Console.

The items on the table should look familiar from the previous lab. The status of items on the **Orders** table may vary if you performed additional simulations during the previous lab on change data capture using DynamoDB streams.

![Orders Table Items](/images/4/4.4/10.png)

Also explore the orders on the **OrdersHistory** table. The number of items on the table will depend on the number of updates you made to the Orders table during the previous lab.

Update the status of order ID **9844720** from **PLACED** to **COMPLETE** using the command below.

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

The output should be similar to the one below.

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

View the items on the **Orders** and **OrdersHistory** tables to see the effects of item update you performed.

The status of order ID **9844720** on the **Orders** table should be **COMPLETE** as shown in the image below.

![Orders Table Items](/images/4/4.4/11.png)

... and there should be a single record on the **OrdersHistory** showing the previous state of order ID **9844720**.

![OrdersHistory Table Items](/images/4/4.4/12.png)

Perform additional updates FOR order ID **9953371** on the Orders table. Start by changing the status of the order to **ACTIVE** then perform another update by setting the status of the same order to **CANCELLED** using the commands below.

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

Followed by

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

Explore the items on the **Orders** and **OrdersHistory** tables to see the result of your updates. The status for order ID **9953371** should be updated on the Orders table and there should be two items on the OrdersHistory table for order ID **9953371**.

![Orders Table Items](/images/4/4.4/13.png)

... and there should be two entries for order ID **9953371** on the **OrdersHistory** table showing the previous states of the order.

![OrdersHistory Table Items](/images/4/4.4/14.png)

{{%notice tip%}}
The order of updates to the Orders table is not preserved by Kinesis Data streams when changes are sent to the create order history lambda function. If you need to record the sequence that updates were made to items on the Orders table, you can configure the precision of the ApproximateCreationDateTime for your Kinesis Data stream. Once this is done, stream records written to your Kinesis Data stream will have an approximate creation date time attribute that can be used to record the sequence of updates to items on the Orders table. See [How Kinesis Data Streams works with DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/kds.html#kds_howitworks)  for more information on how it works.
{{%/notice%}}

