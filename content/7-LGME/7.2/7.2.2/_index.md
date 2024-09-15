---
title : "Build your entity-relationship diagram"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 7.2.2. </b> "
---
The first step of any data modeling exercise is to build a diagram to show the entities in your application and how they relate to each other.

In the application, you have the following entities:

- `User`
    
- `Game`
    
- `UserGameMapping`
    

A `User` entity represents a user in the application. A user can create multiple `Game` entities, and the creator of a game will determine which map is played and when the game starts. A `User` can create multiple `Game` entities, so there is a one-to-many relationship between `Users` and `Games`.

Finally, a `Game` contains multiple `Users` and a `User` can play in multiple different `Games` over time.

Thus, there is a many-to-many relationship between `Users` and `Games`. You can represent this relationship with the `UserGameMapping` entity.

With these entities and relationships in mind, the entity-relationship diagram is shown below.

![ERD - Entity Relationship Diagram](/images/7/7.2/1.png)

Next, we will take a look at the access patterns the data model needs to support.
