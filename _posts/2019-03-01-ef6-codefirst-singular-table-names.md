---
title: "Entity Framework 6 Code First - singular table names"
tags:
  - EF6
  - C#
---
By default, EF 6 Code First creates a table in your database with the convention that the table name to be a pluralized version of the entity type name.

EF allows you to remove convention responsible for pluralizing table name. To do that you should remove `PluralizingTableNameConvention` convention in the `OnModelCreating` method of your `DbContext` class. `PluralizingTableNameConvention` is located in the `System.Data.Entity.ModelConfiguration.Conventions` namespace.

~~~ csharp
public class SchoolEntities : DbContext
{
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}
~~~

You can read more about EF6 conventions in the MSDN docs under the link: [https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/conventions/built-in](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/conventions/built-in)
