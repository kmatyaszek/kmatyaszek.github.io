---
title: "Entity Framework Core - preview model"
tags:
  - EFCore
---

In this article, I show you VisualStudio extension [EFCorePowerTools](https://github.com/ErikEJ/EFCorePowerTools) written by Erik Ejlskov Jensen which lets you see how Entity Framework Core interpreting your model. This extension is very helpful to avoid problems when EF Core interprets the model in a different way than you anticipated. EF Core has some conventions that you should follow to receive the desired database schema. With this extension, you don't have to apply the migration to the database to see if you didn't make any mistakes in your model. You can preview the model in the graph or you can generate SQL scripts to see what will be generated in your database.



The first thing we need to install is a DGML editor. I installed it in the Visual Studio Installer in the individual components section.

![DGML editor](/images/posts/dgml_editor.png)

Next, install VS extension [EF Core Power EFCorePowerTools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EFCorePowerTools).

After that, install `Microsoft.EntityFrameworkCore.Design` NuGet package, in the same project as DbContext.

```
PM> Install-Package Microsoft.EntityFrameworkCore.Design
```

And that’s all. Now you can right-click on the project that contains DbContext, go to the EF Core Power Tools and click Add DbContext Model Diagram.

![EF Core Power Tools](/images/posts/efcorepowertools.png)


If your project type does not produce any executable build output you have to add an additional target framework. You can do that by editing csproj file like the following:
```
<PropertyGroup>
  <!--<TargetFramework>netstandard2.0</TargetFramework>-->
  <TargetFrameworks>netcoreapp3.0;netstandard2.0</TargetFrameworks>
</PropertyGroup>
```

You can read more about the target framework in the following link: [https://docs.microsoft.com/en-us/dotnet/standard/frameworks](https://docs.microsoft.com/en-us/dotnet/standard/frameworks).
