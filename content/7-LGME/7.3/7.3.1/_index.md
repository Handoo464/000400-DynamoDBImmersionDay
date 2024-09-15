---
title : "Design the primary key"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 7.3.1. </b> "
---

Let’s consider the different entities, as suggested in the preceding introduction. In the gaming application, you have the following entities:

- `User`
    
- `Game`
    
- `UserGameMapping`
    

A `UserGameMapping` is a record that indicates a user joined a game. There is a many-to-many relationship between `User` and `Game`.

Having a many-to-many mapping is usually an indication that you want to satisfy two `Query` patterns, and this gaming application is no exception. You have an access pattern that needs to find all users that have joined a game as well as another pattern to find all games that a user has played.

If your data model has multiple entities with relationships among them, you generally use a [composite primary](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html#HowItWorks.CoreComponents.PrimaryKey)  key with both _partition key_ and _sort key_ values. The composite primary key gives you the `Query` ability on the _partition key_ to satisfy one of the query patterns you need. In the DynamoDB documentation, the _partition key_ is also called `HASH` and the _sort key_ is called `RANGE`.

The other two data entities — `User` and `Game` — don’t have a natural property for the _sort key_ value because the access patterns on a `User` or `Game` are a key-value lookup. Because a _sort key_ value is required, you can provide a filler value for the _sort key_.

With this in mind, let’s use the following pattern for _partition key_ and _sort key_ values for each entity type.

|Entity|Partition Key|Sort Key|
|---|---|---|
|User|USER#<USERNAME>|#METADATA#<USERNAME>|
|Game|GAME#<GAME_ID>|#METADATA#<GAME_ID>|
|UserGameMapping|GAME#<GAME_ID>|USER#<USERNAME>|

Let’s walk through the preceding table.

For the `User` entity, the _partition key_ value is `USER#<USERNAME>`. Notice that a prefix is used to identify the entity and prevent any possible collisions across entity types.

For the _sort key_ value on the `User` entity, a static prefix of `#METADATA#` followed by the `USERNAME` value is used. For the _sort key_ value, it’s important that you have a value that is known, such as the `USERNAME`. This allows for single-item actions such as `GetItem`, `PutItem`, and `DeleteItem`.

However, you also want a _sort key_ value with different values across different `User` entities to enable [even partitioning](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.Partitions.html)  if you use this attribute as a _partition key_ for an index. For that reason, you append the `USERNAME`.

The `Game` entity has a primary key design that is similar to the `User` entity’s design. It uses a different prefix (`GAME#`) and a `GAME_ID` instead of a `USERNAME`, but the principles are the same.

Finally, the `UserGameMapping` uses the same partition key as the `Game` entity. This allows you to fetch not only the metadata for a `Game` but also all the users in a `Game` in a single query. You then use the `User` entity for the _sort key_ on the `UserGameMapping` to identify which user has joined a specific game.

In the next step, you create a table with this primary key design.