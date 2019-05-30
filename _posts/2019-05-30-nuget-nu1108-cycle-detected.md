---
title: "NuGet - error NU1108: cycle detected"
tags:
  - NuGet
---

Lately, when I wanted to create a sample program with an IdentityServer4 I bumped into a problem with NuGet.

I invoked the following commands:
```
mkdir identityserver4
cd identityserver4
dotnet new is4aspid
dotnet build
```
and after that I received the following error:
![Build failed.](/images/posts/tdYBws8pFl.png)

So I went to NuGet documentation and under the link [NuGet Error NU1108](https://docs.microsoft.com/en-us/nuget/reference/errors-and-warnings/nu1108) I found this:
![nu1108](/images/posts/gTcHkrGmUB.png)

After that, I noticed that the problem is with the project name. Its name was the same as the referenced NuGet package. So to resolve this problem I changed the project name and my sample project was build succeeded.
