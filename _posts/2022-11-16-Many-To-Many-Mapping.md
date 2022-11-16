---
layout: post
title: "Many-To-Many Mapping"
date: 2022-11-16
categories: BackendFrameworks
---




Many-To-Many associations are usually implemented using an intermediate table. This can be done either by hidden linking table automatically created by ORM (“hidden” meaning that we don’t have corresponding entity class in our application code, the table is created automatically in the database only) , or an intermediate entity.  Let’s review what Hibernate and Entity Framework Core offer us in such cases. 

In this example, we are using Employee and Task entity classes. An Employee can have multiple tasks assigned, and a task can have multiple employees working on it.

## 1) Intermediate linking table

In this approach, intermediate table created by the ORM is used to store connections. Since creating the “middleman” is ORM’s responsibility, our responsibility consists of correctly telling it how creation should go. Admittedly, hibernate makes this process a little bit harder, since this is not a default behaviour. Let’s describe this process in both mappers.

### Hibernate

Technically, all we have to do in Hibernate is to annotate both (or one) entities’ collection fields with @ManyToMany. This will work correctly, but often it isn’t what we want. 
Without any further annotations, hibernate creates two separate tables, in our case: “employee_task” and “task_employee” (using MySQL). The two tables have the same columns:  employee_id and task_id. The difference is only foreign keys: task_id is the foreign key in the first, and employee_id is the foreign key in the second. 

While this will work, we can simplify database schema: we will create only one intermediate table, in which the primary key will be a composite key consisting of both entity’s id’s, and each of them will also act as a foreign key. There is the example code:

![MTMHibernate](/assets/MTMHibernate.png)

Inverse join column indicates column with Item entity’s id’s. If we wanted to make it bidirectional, we just have to specify mappedBy on Item’s @ManyToMany and nothing more.

### Entity Framework Core
In my previous post about One-To-One associations, I said said Hibernate’s @JoinTable was a nice feature that (as far as I know) EF Core does not have. While this is true,  in this case EF Core does not need it at all, since creating an intermediate table is the default behaviour, instead of creating two different tables like Hibernate. Here is the SQL for the generated table:

![MTMCSSQL](/assets/MTMCSSQL.png)

If we don’t want to change auto-generated names, we don’t even need to write a single line of code in OnModelCreating method, everything is already configured for us.

## 2)	Using an intermediate entity
Many-To-Many relationship can also be configured using an intermediate entity class. This time we have to create a class in our sourcr code explicitly, with two One-To-Many associations to each entity. This is done using the standard configurations, and since nothing new is introduced which is exclusively for this approach, I won’t detail it here. The only “new” thing is that we have to define composite primary key explicitly, which again happens in a standard way.

## Conclusion
This was a review of many-to-many mapping in Hibernate and Entity Framework Core. It can be said EF Core is easier to set up if we wanted to follow standard approach. If for some reason we wanted to have two separate intermedaite tables, Hibernate’s default behaviour would make things easier for us, but this is (probably) rarely the case. Overall, both of them provide effiecient and clean ways to configure such relationship.  Thanks for reading
