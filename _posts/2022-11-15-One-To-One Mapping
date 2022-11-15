---
layout: post
title: "One-To-One Mapping"
date: 2022-11-15
categories: BackendFrameworks
---

In this post, I will discuss what options Hibernate and Entity Framework Core give us to configure One-To-One Relationships between entities.

There are three main approaches to map one-to-one relationship:
1) Shared primary key
2) Separate foreign key column
3) Intermediate (Joined) table

## Shared primary key

###Hibernate
      
In this strategy, two associated entities share the primary key. In one of them, the primary key is also a foreign key, which references the another. This is a very nice strategy, however the difficulty of this approach is that we are reponsible to correctly assign the same primary key value, and we have to save the entity that has it’s primary key auto-generated (or at least, ALREADY generated before saving the other one) first.
The classes are defined as follows:

![OneToOneClasses](/assets/OTO-SP-Classes.png)


