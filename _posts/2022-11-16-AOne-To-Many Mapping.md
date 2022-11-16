---
layout: post
title: "One-To-Many Mapping"
date: 2022-11-16
categories: BackendFrameworks
---


One-To-Many entity association is arguably the most important and common type of relationship. There is only one standard approach: the “Many” side owns the foreign keys. Let’s review how Hibernate and Entity Framework Core configure such association.

The standard way to configure One-To-Many relationship, no matter if it’s unidirectional or bidirectional, is to have “Many” side of the raltionship own the foreign keys referencing the other entity. 
For this example, lets say we have two entities: Item and Bid. Item can have many bids placed(One-To-Many), and the bid should be associated with one item(Many-To-One)

## Hibernate

Consider this class, having only one (required) annotation: 

![OTMHibernateClass](/assets/OTMHibernateClass.png)

In this case, Item class has only one @OneToMany annotation, with cascade parameter to ensure that it’s associated bids will also be saved with the item. Technically, this is all we need to configure unidirectional one-to-many relationships. Here’s what happens in this case:

1)	Hibernate creates additional table (usually named like “entity1name_entity2name”), in which each row contains two columns: One containing the primary key of the first entity, and as you probably guessed, the other containing the primary key of the second. Entities with primary keys in the same row are associated with each other. The “Many” entity’s column values are unique.
 
2)	Entitities’s own tables have only columns that are solely their own, and hold no reference to the other entity.


However, we can avoid creating the additional table and have the table of “Many” entity (Bid) contain the foreign key values directly. For this, let’s uncomment @JoinColumn annotation. 
But in this case, we come across different behaviour. If you have experience with One-To-One relationships, the @JoinColumn annotation creates an additional column in the table of the entity that has the annotation. But in this case, things are different: the column gets created in the “Many” entity’s table, even though the “One” side is the one that has the annotation. 
In this case, the column would get created in the “bid” table (even though Item was the one that declared the annotation). That’s why  the name of the column is “item_id” – it creates a foreign key column in “bid” that references “item”. This is the main quirk of mapping One-To-Many in the hibernate.
The other annotation, @OrderColumn, just tells hibernate to create a column that will hold the order/position of “Many” entities, according to how they are placed in the collection. Note that @OrderColumn only works for collections that support indexing, like List. If we annotated Set, or a “bag”, it would have no effect. 

#### Bag
We can create a bag with ArrayList, but instead of declaring it with “List” interface, we do it with “Collection” like this:

    Collection<Bid> bids=new ArrayList<>();
  
“Bags” offer superior performance, but they support neither indexing nor sorting, and they don’t check for duplciates either. 

#### Bidirectional
Making this relationship bidirectional comes with additional quirks too, again thnaks to @JoinColumn. Consider this class that makes the relationship bidirectional:
*OTMHibernateClassBid* 
We notice that it has the same @JoinColumn annotation, pointing to the same column. This is no coincidence: The Item’s @JoinColumn created the column, and Bid’s one is telling Hibernate to use this column to hold reference to items fro the bids’ point of view.

## Entity Framework Core
The principle of mapping One-To-Many relationship in EF Core is the same. However, EF Core has a different default behaviour.
As mentioned above, if we don’t explicitly use @JoinColumn annotation one “One” side of the relationship, Hibernate automatically creates an additional table to hold the connecting primary keys. In EF Core, creating a foreign key column is the default behaviour.

![OTOMCSM](/assets/OTOMCSM.png)
 
In this case, we haven’t explicitly configred the foreign key, but foreign key column “ItemId” will get created in “Bid”table, with no additional tables getting created.


