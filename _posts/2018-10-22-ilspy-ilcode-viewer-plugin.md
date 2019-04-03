---
title: "ILSpy - IL code viewer plugin"
categories:
  - projects
tags:
  - ILSpy
---

Recently when I worked with ILSpy I wanted to see the Intermediate Language but I could not find this functionality. So I decided to create a plugin to ILSpy called ILViewer. You can find it on github: [https://github.com/kmatyaszek/ILSpy-ILViewer](https://github.com/kmatyaszek/ILSpy-ILViewer).

You can preview IL code for following members:
* type
* field
* method
* property
* event

This ILViewer plugin functionality you can find in the context menu (menu item with header “Show IL code”):
<img src="/images/posts/ILSpy_2018-09-22_09-46-49.png" class="align-center" alt="">

When you clicked on this menu, a new window shows up with IL code:
<img src="/images/posts/ILSpyILViewer_window.png" class="align-center" alt="">

Based on [issue #1284](https://github.com/icsharpcode/ILSpy/issues/1284) I also extended console tool ilspycmd to support IL viewer code in console (for more information please refer to [PR #1285](https://github.com/icsharpcode/ILSpy/pull/1285)).
