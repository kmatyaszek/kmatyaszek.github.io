---
title: "Entity Framework 6 - optimistic concurrency"
tags:
  - C#
  - EF6
---
Concurrency conflicts occur when more than one user wants to edit the same entity stored in the database at the same time. Let's say that we have two users Mark and John currently using our application and they want to edit the same entity stored in DB which Id is equal to 2 (please refer to image below).

<img src="/images/posts/ef_read_data_by_two_users.png" class="align-center" alt="">

After reading the current state from DB both of them has the exact same copy of this entity.

<img src="/images/posts/ef_local_copy.png" class="align-center" alt="">

And now Mark wants to update information about email and John wants to change the name of the user. Let's assume that Mark is faster in editing data than John and his change is written to the database.

<img src="/images/posts/ef_mark_saved_data.png" class="align-center" alt="">

And now John saves his data without knowledge that Mark changed this data before him.

<img src="/images/posts/ef_john_saved_his_data.png" class="align-center" alt="">

As you can see changes entered by Mark was lost because John was working on the data which was retrieved before Mark was edited it. In this situation, users that are using your software don’t know what’s going on with data they entered to the system. They’re just disappearing without giving any information to the user.

In Entity Framework you can deal with this problem using optimistic concurrency.
You should add to your class property `RowVersion` (this property must be a byte array data type) and also you must add attribute `TimeStamp` to this property.
~~~ csharp
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
    [Timestamp]
    public byte[] RowVersion { get; set; }
}
~~~

If you prefer to use the Fluent API you can use the `IsConcurrencyToken` method:

~~~ csharp
modelBuilder.Entity<User>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
~~~

A `System.Data.Entity.Infrastructure.DbUpdateConcurrencyException` exception is thrown by the `SaveChanges` method when an optimistic concurrency exception is detected.

You can handle this exception in multiple ways e.g. database wins, client wins or you can create a custom resolution of optimistic concurrency exception. For your custom implementation, you can prevent change from being updated in the database. You can display an error message and show to the user the current state of the data and allow him to make a decision what want to do with this data. For further information please visit the following page [http://go.microsoft.com/fwlink/?LinkId=472540](http://go.microsoft.com/fwlink/?LinkId=472540)
