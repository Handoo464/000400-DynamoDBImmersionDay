---
title : "Exercise 5: Sparse Global Secondary Indexes"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 3.6. </b> "
---
You can use a sparse global secondary index to locate table items that have an uncommon attribute. To do this, you take advantage of the fact that table items that do not contain global secondary index attribute(s) are not indexed at all.

Such a query for table items with an uncommon attribute can be efficient because the number of items in the index is significantly lower than the number of items in the table. In addition, the fewer table attributes you project into the index, the fewer write and read capacity units you consume from the index.