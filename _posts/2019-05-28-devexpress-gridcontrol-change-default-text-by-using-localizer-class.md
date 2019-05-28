---
title: "DevExpress GridControl - change default text by using Localizer class"
tags:
  - WPF
  - DevExpress
---

If you want to translate default text e.g. “Click here to add a new row” in the `GridControl` you can use a Localizer class. Each DevExpress component has a specific Localizer class but not all string can be translated via that class.

First, you should create a custom class where you can change default localization by invoking `AddString` method:

~~~ csharp
public class CustomGridLocalizer : GridControlLocalizer
{
     protected override void PopulateStringTable()
     {
         base.PopulateStringTable();
         AddString(GridControlStringId.GridNewRowText, "Click here to add a new customer");
     }
 }
~~~

Next, you should create an instance of this class in the view constructor and assign it to the `GridControlLocalizer.Active`, just like below:

``` csharp
public partial class MainView : UserControl
{
     public MainView()
     {
         InitializeComponent();
         GridControlLocalizer.Active = new CustomGridLocalizer();
     }
}
```

The full list of `GridControlStringId` enumeration with a default value and description you can find under the following link  [GridControlStringId Enumeration](https://documentation.devexpress.com/WPF/DevExpress.Xpf.Grid.GridControlStringId.enum).
