---
title: "Secure static files in ASP.NET Core"
tags:
  - ASP.NET Core
---

The Static File Middleware doesn't provide authorization, all files served by this middleware are publicly accessible. Static files are stored in the following directories:
* Web root directory - `{content root}/wwwroot` - it's the default directory
* you can also serve files outside the Web root directory by specifying it in the `UseStaticFiles` method as below:
``` csharp
public void Configure(IApplicationBuilder app)
{
    app.UseStaticFiles();

    app.UseStaticFiles(new StaticFileOptions
    {
        FileProvider = new PhysicalFileProvider(
            Path.Combine(Directory.GetCurrentDirectory(), "PublicFiles")),
        RequestPath = "/pf"
    });
}
```

So if you want to serve files with authorization you should:
1. make sure that your file is not in any directory or subdirectory specified in the `UseStaticFiles` methods,
2. serve it via an action method in which the `Authorize` attribute is applied.

The following code is an example of such action:

``` csharp
[Authorize]
public IActionResult GetSecretDocument()
{
    var file = Path.Combine(Directory.GetCurrentDirectory(),
        "SecureFiles", "secret.pdf");

    return PhysicalFile(file, "application/pdf");
}
```

The complete example you should find on the [GitHub repository](https://github.com/kmatyaszek/AspNetCoreAuthorizeStaticFile).
