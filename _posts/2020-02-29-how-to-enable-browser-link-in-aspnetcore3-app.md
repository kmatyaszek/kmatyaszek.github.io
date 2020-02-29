---
title: "How to enable Browser Link in ASP.NET Core 3.1"
tags:
  - ASP.NET Core
---
This guide will step you through the process of enabling Browser Link in ASP.NET Core project.

#### What is Browser Link?

Browser Link is a Visual Studio feature that helps you develop web apps in several browsers at once. If you using this feature you don't have to rebuild your project and rerun browser to see changes that you made in views.

Under the hood Browser Link uses SignalR to create a communication channel between the browser and VS.  Visual Studio acts as a SignalR server and browsers are clients and can connect to VS at the same time.  When Browser Link is enabled it will register a middleware in the ASP.NET Core request pipeline. This middleware injects script into every page request dynamically.

#### Follow these instructions to enable Browser Link.

1) Install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink) Nuget package
```
PM> Install-Package Microsoft.VisualStudio.Web.BrowserLink
```

2) Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) Nuget package
```
PM> Install-Package Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation
```

3) Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.
``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages().AddRazorRuntimeCompilation();
}
```

4) Call `UseBrowserLink` in the `Startup.Configure` method:
```
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
   if (env.IsDevelopment())
   {
       app.UseDeveloperExceptionPage();
       app.UseBrowserLink();
   }
   // code deleted for brevity
}
```
5) Select browsers from the following menu and run your project.
![Select browsers](/images/posts/selectwebbrowser.png)

Now you can start using Browser Link in your project.
