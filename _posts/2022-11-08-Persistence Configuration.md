---
layout: post
title: "Persistence Configurations"
date: 2022-11-08
categories: BackendFrameworks
---

Hibernate and Entity Framework Core are gold standards of Object-Relational Mapping in Spring and ASP.NET Core applications, respectively. In Fact, when we are talking about persistence and everything it comprises in these frameworks, we are implying these ORM’s. Often, “Persistence in Spring” and “Hibernate” are interchangeable, and the same goes for ASP.NET Core and EF Core. 

In the subsequent posts, I want to write about working with these tools. However, before I start writing about entity relationships, transactions and other important subjects, I want to sum up how these ORM’s can be configured. 
In this case, by “configuration”, I don’t mean setting up the connection, where to write connection strings, which classes are used for configuring these things and so on. Skipping over that, I want to write about configuring entities and their relationships. 
Both Hibernate and EF Core are ORM’s, therefore they serve the same purpose. However, the way they configure entities are different, so I wanted to sum up these differences and what options they provide. I have to say, I have much more to say about EF Core than Hibernate, not because it is better, but it provides more options of doing things (things which Hibernate can absolutely do as well).

### Hibernate
Most of Hibernate’s entity configurations are done with annotations. Hibernate provides robust and intuitive way of defining relationships. Entities themselves are annotated with @Entity, with their primary key annotated with @Id. There are many other annotations that we can use, such as @Column, @GeneratedValue to extend functionality. (The concrete purpose of these annotations are not the subject of this post).
Relationships are mapped with @OneToOne, @OneToMany and @ManyToMany. Hibernate does not need us to explicitly declare if a relationship is unidirectional or bidirectional. If one entity contains a field with one of those annotations referencing another entity, while the other entity does not, then the relationship is unidirectional. If the referenced entity also has corresponding field and annotation, then the relationship becomes bidirectional. (Often we need additional steps to configure bidirectional relationship, but this post is not about that). Hibernate is a powerful tool which requires few lines to configure correctly. Of course, you have to comprehend the concept of entity relationships and your current needs fully and correctly. 
Many of us have solved complex mathematical problems and spent great deal of time just thinking and writing only few lines of equations. Often times, you can solve a problem just by writing few lines, but in order to write those few lines correctly, you have to take your time to think and understand the problem fully. In short, you may spend an hour thinking about the ways to solve a problem, and write a solution in half a minute. Working with Hibernate (and to some extent, the same can be said about EF Core) is one of those cases. If you think about what your program needs and come up with solutions, you can put those solutions into action by writing a few lines of code, most of which can be annotations. 

### Entity Framework Core
EF Core provides much more ways to define your entities and how they behave than Hibernate. (Once again, that does not mean that one is better than the other). We can configure relationships with three approaches: using conventional approach, using annotations, and using Fluent API. 

#### Conventional 
Conventional, or By-Convention approach is more of a straightforward way, and can save us a lot of time when we just need easy and non-complex relationship configured. Basically, we have to define properties of other entities that we want our entity to reference, and EF Core will work out the rest. However, we have to consider one important thing: If the referenced entity also has corresponding property that references our entity, EF Core can figure out what kind of relationship they have (is it one-to-one or one-to-many for example), but if the referenced entity does not have such property, the mapper can’t be sure, so by default it assumes that the relationship is one-to-many. 
As easy and straightforward this approach is, it also has drawbacks. For example, we might need to have unidirectional one-to-one relationship (as stated above, in case of only one entity having referencing property, one-to-many is assumed by default), or we may need composite foreign keys, which we need to configure manually. 

#### Annotations
By using annotations, we gain the ability to explicitly define which foreign key EF Core should use. Also, in case one entity has two different properties of the same entity class, we can use InverseProperty annotation to link the properties correctly.

#### Fluent API
Fluent API is a great way to define entity relationships, and as someone who likes explicitly defining such things, is the one try to always use. Relationships defined this way are written in Database Context class’s OnModelCreating(ModelBuilder builder) method, which is handy in a way that you all of the logic explicitly defined in one place, which you can check and update anytime. Basic Fluent API code looks like this:
`builder.Entity<City>().HasOne<Country>(e=>e.Country).WithMany(e=>e.Cities).HasForeignKey(p=>p.CountryId);`
Which makes it clear what kind of relationship entities have and what keys they use. 



### Conclusion
This post was meant to be a review of how configuration works in these ORM’s. Discussing the differences and contrast of tools such as ORM’s is always interesting, and often I don’t like to say that one tool, be it ORM, framework or a programming language, is better than the other. However, if we were to compare these two object-relational mappers and wanted to say “which is better” for whatever reason, configuration quirks are not a good way to evaluate their worth. Once again, this post was just a review of their general configuration methods. How they configure and define concrete cases is much more interesting, and I will write about it the next posts. Thanks for reading 😊
