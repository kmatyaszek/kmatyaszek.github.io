---
title: "Entity Framework 6 - speed up reading the data"
tags:
  - EF6
  - C#
---
If you want to read data from the database without need of editing it later you can improve your query by using the `AsNoTracking` method. This "read-only query" is quicker to execute because EF will no need to set up change tracking information about every single loaded entity.

To show you benefits from using `AsNoTracking` method I created a sample program. In my sample program, I read data from *AdventureWorks* database from the single table *TransactionHistory* (in this table is 113443 rows).  To measure performance I was using [BenchmarkDotNet](https://github.com/dotnet/BenchmarkDotNet).

Below piece of code presents two methods which are compared in my test.

~~~ csharp
public void Get()
{
    using (var ctx = new AdventureWorks2017Entities())
    {                        
        var tmp = ctx.TransactionHistories.ToList();
    }
}

public void GetAsNoTracking()
{
    using (var ctx = new AdventureWorks2017Entities())
    {
        var tmp = ctx.TransactionHistories.AsNoTracking().ToList();
    }
}
~~~

The result is pretty good. The arithmetic mean of all measurements with no tracking is 541.4 ms versus  3,601.9 ms with change tracking functionality. Of course, allocated memory per single operation is also smaller in the method without tracking changes respectively 30.92 MB vs 214.12 MB.

If you are interested in the full BenchmarkDotNet report for this test please go to this site: [https://gist.github.com/kmatyaszek/fb7033c1baa546f94c7bcf0d39f14747](https://gist.github.com/kmatyaszek/fb7033c1baa546f94c7bcf0d39f14747)
