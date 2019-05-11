---
title: ".NET Core 3 preview - not working in VS2019 main release"
tags:
  - .NETCore3
---

If you have installed VS2019 main release (version 16.0.\*) you cannot create a new project with target platform .NET Core 3 by default. To make it possible you have two options:

* You can change settings in currently installed VS2019 (`Tools -> Options -> Projects and Solutions -> .NET Core -> Use Previews of the .NET Core SDK`)

![VS2019 settings](/images/posts/R8X1Tp2ik9.png)

After that, you should restart Visual Studio. If it’s not working please sure that you have installed .NET Core 3 on your machine using the following command:

```
dotnet --version
```
Above command should return version 3 (in my case it's 3.0.100-preview5-011568).

* Or you can install VS2019 in preview version (it’s recommended options). You can install the preview version of Visual Studio alongside the main release without any impact of each other. Visual Studio in preview version has better support for .NET Core 3.0 for more information please visit [Visual Studio 2019 Preview Release Notes](https://docs.microsoft.com/en-us/visualstudio/releases/2019/release-notes-preview).
