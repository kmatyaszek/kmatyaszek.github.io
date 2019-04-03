---
title: "DevExpress WPF Themes - how to apply theme to an entire application"
tags:
  - WPF
  - DevExpress
---
DevExpress theme manager has an important property `ApplicationThemeName`. This property allows you to set global theme applied to the entire application. Without settings this property, you have to specify theme name on every new window in your project.

Let's say that you have following XAML code (in this example `ApplicationThemeName` is not set on application startup, only `ThemeName` is set in the main window):

~~~ xml
<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800"
        xmlns:dxc="http://schemas.devexpress.com/winfx/2008/xaml/core"
        dxc:ThemeManager.ThemeName="DeepBlue">
    <Grid>
        <Button Name="btnOpenChildWindow" Content="Open child window"
                Width="200" Height="100" Click="btnOpenChildWindow_Click"/>
    </Grid>
</Window>
~~~

~~~ csharp
namespace WpfApp1
{   
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private void btnOpenChildWindow_Click(object sender, RoutedEventArgs e)
        {
            var childWindow = new ChildWindow();
            childWindow.ShowDialog();
        }
    }
}
~~~

~~~ xml
<Window x:Class="WpfApp1.ChildWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        mc:Ignorable="d"
        Title="ChildWindow" Height="250" Width="350">
    <Grid>
        <Button Content="child window button" Width="200" Height="100" />
    </Grid>
</Window>
~~~

Now if you forget that you have to set `ThemeName` locally to every element, you probably end up with the situation presented below on the screenshot.
<img src="/images/posts/explorer_2018-08-13_10-57-19.png" class="align-center" alt="">
If you set `ApplicationThemeName` on the startup of your application you don't have to remember to set the appropriate theme type to every new visual element in your application. Of course, if you want to change the theme on this new element you can use `ThemeName` property (if this property is used `ApplicationThemeName` isn't applied to this element).

~~~ csharp
using DevExpress.Xpf.Core;
using System.Windows;

namespace WpfApp1
{
    public partial class App : Application
    {
        protected override void OnStartup(StartupEventArgs e)
        {
            ThemeManager.ApplicationThemeName = Theme.DeepBlueName;
            base.OnStartup(e);            
        }
    }
}
~~~
