---
layout: post
title: "Inheritance Mapping"
date: 2022-11-13
categories: BackendFrameworks
---

This article discusses the process of mapping inheritance in Hibernate and Entity Framework Core. Inheritance is not a native thing to relational databases, and RDBMSes don’t support type inheritance, when on the other hand inheritance is one of the most important aspects of Object-oriented programming. Therefore, how ORMs handle this mismatch is critical to the program. Fortunately, both Hibernate and EF Core are very powerful tools that make mapping inheritance easy and intuitive.
I’m gonna jump ahead and say that the process is very similar for both orm’s. As big as this mismatch is between object-oriented and relational platforms, these two tools make mapping it so easy that your main challenge lies not in writing long and complex code, but in fully understanding how different inheritance mapping strategies work. If you have a clear model in your head, you put it into action by writing just a few lies of code.
In this article I will discuss three strategies:

1)	Single table	

2)	Table per concrete subclass (TPC)

3)	Joined Table (TPT)

In .NET Core terminology, "TPC" stands for "Table-per-concrete-type", and "TPT" stands for "Table-per-type"

### Single table strategy
Single table strategy maps the whole class hierarchy to a single table, which includes columns for storing all the data in the hierarchy. Discriminator columns are used to differentiate between rows. Both Hibernate and EF Core choose this strategy as default, if none specified, and provide discriminator columns. Overriding default discriminator properties are just a matter of annotation(Hibernate) or a single method in OnModelCreating method (EF Core). 
By using this strategy, not only you simplify the database structure by having only one table, but also benefit from performance bonus compared to other strategies, because queries perform much faster. However, like many other things in programming, this approach also has drawbacks.
Before I move on to the main problem of this approach, I have to say that having many differend kinds of objects in one table makes in bloated, unintuitive and harder to work with. For example let’s say we have abstract Item class for online shopping, which has subclasses of books, laptops, PC items, many different kinds of clothes and so on. Having all these completely different items in a single table seems… not good. If we wanted to work with only one kind of data, we have no choice but to make operations to this table, when in other strategies we could’ve used only the corresponding table and left others alone. 
But the main prolem of this approach is that we are forced to make properties nullable. A single item can’t have data in all of the columns (which belong to other items), therefore those columns have to be made null. This can be a serious problem, because we can’t enforce non-null constraint even when the data has to be provided. In this case, extreme caution is needed to ensure the data is correct.



### Table per concrete subclass/TPC
In this strategy, all concrete types are mapped to individual tables, and they inherit all the persistent properties from superclass. In a typical scenario, superclass in an abstract class that defines the rules for identifier generation, as well as the properties that are common to all the subclasses. 
Given that we have appropriate fields written correctly, Implementing this strategy is extremely easy in both ORMs.
In Hibernate, all we have to do is:

1)	Annotate the superclass with @Inheritance(strategy=InheritanceType.TABLE_PER_CLASS)

2)	Annotate the subclasses with @Entity

In EF Core:

1)	Define every class in the hierarchy by DbSet<Entity> in DbContext class

2)	Define the strategy in OnModelCreating method by modelBuilder.Entity<Superclass>().UseTpcMappingStrategy()

  Since all of those configuration happens by just a few lines of code, we can easily swich between TPC and TPT strategies in development.

### Table per subclass with joins/Joined Table/TPT
In this strategy, all the classes, including abstract ones(and even interfaces), are mapped to their own individual tables, and the properties that belong solely to an entity are mapped in the entity’s own table. Although we don’t have to define identifier generators for subclasses, their tables still contain Id columns, which are inherited from superclass and serve as foreign keys to the base (superclass) table.
Although this strategy is very different, implementing it is very similar to the TPC strategy. The steps are similar as described above, except:

1)	In Hibernate, we annotate superclass with @Inheritance(strategy=InheritanceType.JOINED)

2)	In EF Core, we use UseTptMappingStrategy() instead of UseTpcMappingStrategy()

Just like I said in the beginning of this article, both of these ORMs make it very easy to write a code for desired strategy, and your main objective is to understand how these strategies work conceptually. 

### Conclusion
The fact that both Hibernate and EF Core are high quality tools is undeniable, and is only solidified by how easy it is to use desired inheritance mapping strategy. This article does not discuss the performances of these strategies, rather the process of implementing them in your program, which is made easy by both of these tools. Thanks for reading 😊 
