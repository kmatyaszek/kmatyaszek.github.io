---
title: "C# 6 feature - using static directive"
tags:
  - C# 6
---
In C# 6 was introduced `using static` directive. By using this language construct you can reference to static class members without specifying a type name.

Usage:
``` csharp
using static <fully-qualified-type-name>;
```

This feature can be really helpful when you want to create a simple console application to test something. Instead of using `Console.***` all the time you can speed up the development process by using `using static System.Console` and after that, you can use static methods defined in the `Console` class.

In the following examples you can see simple app without and with using `using static` directive.

~~~ csharp
using System;
static void Main(string[] args)
{
    Console.WriteLine("Please enter some number:");
    var input = Console.ReadLine();
    if (!string.IsNullOrEmpty(input) && int.TryParse(input, out var number))
    {
        if (number > 0)
            Console.WriteLine("Positive number");
        else if (number == 0)
            Console.WriteLine("Zero");
        else
            Console.WriteLine("Negative number");
    }
    else
    {
        Console.WriteLine("Invalid input!");
    }
}
~~~

~~~ csharp
using static System.Console;
using static System.String;
static void Main(string[] args)
{
    WriteLine("Please enter some number:");
    var input = ReadLine();
    if (!IsNullOrEmpty(input) && int.TryParse(input, out var number))
    {
        if (number > 0)
            WriteLine("Positive number");
        else if (number == 0)
            WriteLine("Zero");
        else
            WriteLine("Negative number");
    }
    else
    {
        WriteLine("Invalid input!");
    }
}
~~~

Another interesting case can be class `Math` which is static class. Let's say that you want to calculate area of the circle. Without using `using static` directive you write following code `Math.PI * Math.Pow(Radius, 2)` but when you add to your using statements `using static System.Math;` you can write previous code as `PI * Pow(Radius, 2)`.

Let's jump to another example. We want to calculate the area of a circular segment. As you will see in the example using `using static` directive improve math formula readability. We're going to use the following formula to calculate.

\\[A = {\alpha \over 360} \pi r^2  - {r^2 \sin(\alpha) \over 2} \\]

Below is implementation of this formula in C#. First of them using `using static` directive (the code is simpler and more readable than the other one).

~~~ csharp
using static System.Math;
...
double degress = 120.0;
double radius = 8.0;
double angle = PI * degress / 180.0;
var area = ((degress / 360.0) * PI * Pow(radius, 2)) - (Pow(radius, 2) * Sin(angle) / 2);
~~~

~~~ csharp
double degress = 120.0;
double radius = 8.0;
double angle = PI * degress / 180.0;
var area = (degress / 360.0) * Math.PI * Math.Pow(radius, 2))
            - ((Math.Pow(radius, 2) * Math.Sin(angle)) / 2);
~~~
