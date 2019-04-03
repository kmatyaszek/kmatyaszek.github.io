---
title: "Improve built-in ASP.NET Core DI container with Scrutor"
tags:
  - ASP.NET CORE
  - SCRUTOR
---

ASP.NET Core has built-in dependency injection (DI) container. It's easy and simple for the smaller project where you register all your dependencies (e.g. services, repositories, e.t.c.) by hand. Microsoft.Extensions.DependencyInjection has not built-in type scanning functionality, so if your project will grow, using it can be pretty hard. But you can resolve this problem by using [Scrutor](https://github.com/khellang/Scrutor). Scrutor is an extension for built-in DI that allows you scan assembly and decorate services. In this article, we will look at scanning functionality.

*Problem:*
You want to register all classes from the specific assembly (e.g. all repositories).

To resolve this problem in an efficient way, you need to install Scrutor to your project ([NuGet Package](https://www.nuget.org/packages/Scrutor)).

### Package Manager Console
~~~
Install-Package Scrutor
~~~

When you have successfully installed Scrutor you can use it by invoking method Scan on the IServiceCollection collection in the ConfigureServices method.

~~~ csharp
services.Scan(scan => scan
     .FromCallingAssembly()
     .AddClasses(classes => classes.InNamespaces("SampleWebAppScrutor.Repositories"))
     .AsImplementedInterfaces()
     .WithTransientLifetime()
);
~~~

Let's dive into this example:
* FromCallingAssembly - scan the calling assembly
* AddClasses - get classes only from namespace "SampleWebAppScrutor.Repositories"
* AsImplementedInterfaces - specify what type you want to register these classes are
* WithTransientLifetime - specify the lifetime of registrations

So instead of registering your service by hand like this:

~~~ csharp
services.AddTransient<IUserRepository, UserRepository>();
services.AddTransient<ICustomerRepository, CustomerRepository>();
services.AddTransient<I...Repository, ...Repository>();
~~~

you can write code that will register all repositories in your project without the need of specifying them by hand in ConfigureServices method.

In the GitHub you can find a sample project: [https://github.com/kmatyaszek/SampleWebAppScrutor](https://github.com/kmatyaszek/SampleWebAppScrutor)
