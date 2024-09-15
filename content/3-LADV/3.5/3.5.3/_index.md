---
title : "Step 3 - Query the employees table using the global secondary index with overloaded attributes"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.5.3. </b> "
---
You can query all the employees based in Washington state (WA) in the United States by running the following `query_employees.py` script, which includes code to query a table by using global secondary index overloading.

```py
if attribute == 'name':
    ke = Key('GSI_1_PK').eq('root') & Key('GSI_1_SK').eq(value)
else:
    ke = Key('GSI_1_PK').eq(attribute + "#" + value)

response = table.query(
    IndexName='GSI_1',
    KeyConditionExpression=ke
    )
```

Run the following python script to retrieve the employees based in Washington state (WA).

```bash
python query_employees.py employees state 'WA'
```

The script should give you output that looks like the following.

```txt
List of employees with WA in the attribute state:
    Employee name: Alice Beilby - hire date: 2014-12-03
    Employee name: Alla Absalom - hire date: 2015-06-25
    Employee name: Alvan Heliar - hire date: 2016-05-15
    Employee name: Anders Galtone - hire date: 2015-12-22
    Employee name: Ashil Hutchin - hire date: 2015-02-11
  ...
  Employee name: Sula Prattin - hire date: 2014-01-11
    Employee name: Vittoria Edelman - hire date: 2014-10-01
    Employee name: Willie McCuthais - hire date: 2015-05-27
Total of employees: 46. Execution time: 0.13477110862731934 seconds
```

You can try a different US state by changing the last parameter of the command. The following command queries all employees based in Texas.

```bash
python query_employees.py employees state 'TX'
```

If you want to query other states, click here to open the list of US states with some data in the table

Using the same query, you can query the employees by job title. Run the following command as an example.

```bash
python query_employees.py employees current_title 'Software Engineer'
```

The preceding command will give you the following results.

```txt
 List of employees with Software Engineer in the attribute current_title:
    Employee name: Alice Beilby - hire date: 2014-11-03
    Employee name: Anetta Byrne - hire date: 2017-03-15
    Employee name: Ardis Panting - hire date: 2015-08-06
    Employee name: Chris Randals - hire date: 2016-10-27
    Employee name: Constantine Barendtsen - hire date: 2016-06-10
    Employee name: Eudora Janton - hire date: 2015-01-05
    Employee name: Florella Allsep - hire date: 2015-03-31
    Employee name: Horatius Trangmar - hire date: 2013-10-21
    Employee name: Korey Daugherty - hire date: 2016-11-03
    Employee name: Lenka Luquet - hire date: 2014-10-01
    Employee name: Leonora Hyland - hire date: 2016-06-14
    Employee name: Lucretia Ruffell - hire date: 2015-07-04
    Employee name: Malcolm Adiscot - hire date: 2014-04-17
    Employee name: Melodie Sebire - hire date: 2013-08-27
    Employee name: Menard Ogborn - hire date: 2014-06-27
    Employee name: Merwyn Petters - hire date: 2014-06-19
    Employee name: Niels Buston - hire date: 2014-10-30
    Employee name: Noelani Studde - hire date: 2015-03-30
Total of employees: 18. Execution time: 0.11937260627746582 seconds
```

You also can try a different title, as shown in the following python command.

```bash
1
python query_employees.py employees current_title 'IT Support Manager'
```

If you want to know the list of all the available titles, click here!

Using the same query with one change, you can query the employees by name, as shown in the following command.

```bash
python query_employees.py employees name 'Dale Marlin'
```

The preceding command should give you the following result.

```txt
 List of employees with Dale Marlin in the attribute name:
    Employee name: Dale Marlin - hire date: 2014-10-19
Total of employees: 1. Execution time: 0.1274700164794922 seconds
```

#### Summary

Congratulations, you have completed this exercise and demonstrated how the global secondary index key overloading design can support multiple access patterns. Use this pattern to fit different entity types in the same DynamoDB table and retain the ability to query the data on different partition keys with global secondary indexes. In the next exercise, you will learn about Sparse Global Secondary Indexes!