---
title: "Extended WPF Toolkit - PropertyGrid AdvancedOptionsMenu - copy button"
tags:
  - WPF
---

In this post, I will show you how you can add copy button to [WPF Toolkit PropertyGrid](https://github.com/xceedsoftware/wpftoolkit/wiki/PropertyGrid). To do this we are going to use AdvancedOptionsMenu property. In the PropertyGrid you should set ShowAdvancedOptions to True and add context menu to AdvancedOptionsMenu:

~~~ xml
<Window x:Class="Q44727042.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:xctk="http://schemas.xceed.com/wpf/xaml/toolkit"
        Title="MainWindow" Height="400" Width="500"
        Loaded="Window_Loaded">
    <Grid>
        <xctk:PropertyGrid x:Name="customerEditor" ShowAdvancedOptions="True">
            <xctk:PropertyGrid.AdvancedOptionsMenu>
                <ContextMenu>
                    <MenuItem Name="miCopyProperty" Header="Copy" Click="miCopyProperty_Click" />
                </ContextMenu>
            </xctk:PropertyGrid.AdvancedOptionsMenu>            
        </xctk:PropertyGrid>
    </Grid>
</Window>
~~~

In the MenuItem click event handler, you can access property data by property DataContext in sender object.
~~~ csharp
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
    }

    private void Window_Loaded(object sender, RoutedEventArgs e)
    {       
        customerEditor.SelectedObject = new Customer()
        {
            Id = Guid.NewGuid(),
            Name = "John",
            Surname = "Smith",
            Age = 21,
            IsActive = true,
            AddTime = DateTime.Now
        };            
    }

    private void miCopyProperty_Click(object sender, RoutedEventArgs e)
    {
        MenuItem menuItem = sender as MenuItem;
        if (menuItem != null && menuItem.DataContext is PropertyItem)
        {
            Clipboard.SetData(DataFormats.Text, ((PropertyItem)menuItem.DataContext).Value);
        }
    }
}
~~~

In this example, I create sample class Customer. As you can see below property Id is read-only and others properties can be edited in PropertyGrid. Copy functionality works for both types of property read-only and not read-only.

~~~ csharp
public class Customer
{
    [ReadOnly(true)]
    public Guid Id { get; set; }

    public string Name { get; set; }

    public string Surname { get; set; }

    public int Age { get; set; }

    public bool IsActive { get; set; }

    public DateTime AddTime { get; set; }
}
~~~

In the following screenshot, you can see the result of this code.

<img src="/images/posts/Q44727042_2018-08-22_10-35-21.png" class="align-center" alt="">

In the above example is a problem when you change SelectedObject. It's a bug in PropertyGrid. When you assign a new instance of the class to SelectedObject property you should each time create items in AdvancedOptionsMenu. The following extension method resolve the above problem.

~~~ csharp
public static class PropertyGridEx
{
    public static void AddCopyButtons(this PropertyGrid propertyGrid)
    {
        propertyGrid.ShowAdvancedOptions = true;
        propertyGrid.SelectedObjectChanged += PropertyGrid_SelectedObjectChanged;
    }

    private static void PropertyGrid_SelectedObjectChanged(object sender, RoutedPropertyChangedEventArgs<object> e)
    {
        PropertyGrid propertyGrid = sender as PropertyGrid;
        if(propertyGrid != null)
        {
            propertyGrid.AdvancedOptionsMenu = CreateCopyContextMenu();
        }
    }

    private static ContextMenu CreateCopyContextMenu()
    {
        var contextMenu = new ContextMenu();

        var miCopyValue = new MenuItem() { Header = "Copy value" };
        miCopyValue.Click += MiCopyValue_Click;

        var miCopyValueWithPropertyName = new MenuItem() { Header = "Copy value with property name" };
        miCopyValueWithPropertyName.Click += MiCopyValueWithPropertyName_Click;

        contextMenu.Items.Add(miCopyValue);
        contextMenu.Items.Add(miCopyValueWithPropertyName);

        return contextMenu;
    }

    private static void MiCopyValueWithPropertyName_Click(object sender, RoutedEventArgs e)
    {
        MenuItem menuItem = sender as MenuItem;
        if (menuItem != null && menuItem.DataContext is PropertyItem)
        {
            var propertyItem = (PropertyItem)menuItem.DataContext;
            Clipboard.SetData(DataFormats.Text, $"{propertyItem.DisplayName}: {propertyItem.Value}");
        }
    }

    private static void MiCopyValue_Click(object sender, RoutedEventArgs e)
    {
        MenuItem menuItem = sender as MenuItem;
        if (menuItem != null && menuItem.DataContext is PropertyItem)
        {
            Clipboard.SetData(DataFormats.Text, ((PropertyItem)menuItem.DataContext).Value);
        }
    }
}
~~~

Using this extension method is very simple:

~~~ xml
<Window x:Class="Q44727042.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:xctk="http://schemas.xceed.com/wpf/xaml/toolkit"
        Title="MainWindow" Height="400" Width="500"
        Loaded="Window_Loaded">
    <StackPanel>     
        <xctk:PropertyGrid x:Name="customerEditor" />
    </StackPanel>
</Window>
~~~

~~~ csharp
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
    }

    private void Window_Loaded(object sender, RoutedEventArgs e)
    {
        customerEditor.AddCopyButtons();

        customerEditor.SelectedObject = new Customer()
        {
            Id = Guid.NewGuid(),
            Name = "John",
            Surname = "Smith",
            Age = 21,
            IsActive = true,
            AddTime = DateTime.Now
        };            
    }
}
~~~
