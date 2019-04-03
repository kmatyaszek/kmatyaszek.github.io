---
title: "Entity Framework 6 Code First - add constrains"
tags:
  - EF6
  - C#
---
EF6 has two ways to where you can add constraints to your model. One of them is Fluent API and the other one is Data Annotations.

In this blog post, we will look into the Fluent API. When you use Fluent API you keep your model class very clean.  You don't have to add any attributes to properties in your model class.

Let's say that we have a class `Student` and we want to add constraints to `LastName` property. This property should be not null and should have not more than 100 characters.

~~~ csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
}
~~~

To fulfill your requirements about `LastName` property we should add the following code to the `OnModelCreating` method in your `DbContext` class.

~~~ csharp
public class SchoolContext : DbContext
{
    public SchoolContext() : base("SchoolContext")
    {
    }

    public DbSet<Student> Students { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
    	modelBuilder.Entity<Student>()
    		.Property(s => s.LastName)
    		.IsRequired()
    		.HasMaxLength(100);
    }
}
~~~

As you can see in the previous example you can add constraints directly in the `OnModelCreating` method but if you have a lot of them it would be a little illegible. Entity Framework allows you to create a separate class with model configuration. This new class should inherit from `EntityTypeConfiguration`. Below you can see configuration class for the `Student` class.

~~~ csharp
public class StudentEntityConfiguration: EntityTypeConfiguration<Student>
{
    public StudentEntityConfiguration()
    {
           Property(p => p.LastName)
          	 .IsRequired()
             .HasMaxLength(100);
    }
}
~~~

As you can see above, this code is very similar to this one from the `OnModelCreating` method. If you have created this custom configuration class, in the next step you need to add this class to `Configurations` property of the `DbModelBuilder` in the `OnModelCreating` method.

~~~ csharp
public class SchoolContext : DbContext
{
    public SchoolContext() : base("SchoolContext")
    {
    }

    public DbSet<Student> Students { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
    	modelBuilder.Configurations.Add(new StudentEntityConfiguration());
    }
}
~~~

And that's it. Now you can create separate configuration classes to increase the readability and maintainability of your code.
