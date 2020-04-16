---
title: "WPF - create custom button with image (ImageButton)"
tags:
  - WPF
---

In this blog post, I will show you how you can create a custom control ImageButton. In this control, we will use Grid control to locate our image and the content of the button will be always in the center of it. You don't have to specify the button content - in this case it will be an image button only. ImageButton inherits from Button so you can access all button's properties. Additionally, you can set the following properties:
* ImageSource
* ImageLocation (Left, Top, Right, Bottom, Center)
* ImageWidth
* ImageHeight

On my GitHub you can see demo project: [WpfImageButton](https://github.com/kmatyaszek/WpfImageButton)

Sample code how you can use this control:

``` xml
<local:ImageButton Width="200" Height="50" Content="Click Me!"
    ImageSource="ok.png" ImageLocation="Left" ImageWidth="20" ImageHeight="25" />
```

Steps to create this custom control:

1) Create `Custom Control (WPF)` and give it the name "ImageButton.cs".

![Create new custom constrol](/images/posts/wpfimgbtn_customcontrol.png)

2) Paste the following code into “ImageButton.cs” file.
``` csharp
public class ImageButton : Button
{
   static ImageButton()
   {
       DefaultStyleKeyProperty.OverrideMetadata(typeof(ImageButton), new FrameworkPropertyMetadata(typeof(ImageButton)));
   }

   public ImageButton()
   {
       this.SetCurrentValue(ImageButton.ImageLocationProperty, WpfImageButton.ImageLocation.Left);
   }

   public int ImageWidth
   {
       get { return (int)GetValue(ImageWidthProperty); }
       set { SetValue(ImageWidthProperty, value); }
   }

   public static readonly DependencyProperty ImageWidthProperty =
       DependencyProperty.Register("ImageWidth", typeof(int), typeof(ImageButton), new PropertyMetadata(30));

   public int ImageHeight
   {
       get { return (int)GetValue(ImageHeightProperty); }
       set { SetValue(ImageHeightProperty, value); }
   }

   public static readonly DependencyProperty ImageHeightProperty =
       DependencyProperty.Register("ImageHeight", typeof(int), typeof(ImageButton), new PropertyMetadata(30));

   public ImageLocation? ImageLocation
   {
       get { return (ImageLocation)GetValue(ImageLocationProperty); }
       set { SetValue(ImageLocationProperty, value); }
   }

   public static readonly DependencyProperty ImageLocationProperty =
       DependencyProperty.Register("ImageLocation", typeof(ImageLocation?), typeof(ImageButton), new PropertyMetadata(null, PropertyChangedCallback));

   private static void PropertyChangedCallback(DependencyObject d, DependencyPropertyChangedEventArgs e)
   {
       var imageButton = (ImageButton)d;
       var newLocation = (ImageLocation?) e.NewValue ?? WpfImageButton.ImageLocation.Left;

       switch (newLocation)
       {
           case WpfImageButton.ImageLocation.Left:
               imageButton.SetCurrentValue(ImageButton.RowIndexProperty, 1);
               imageButton.SetCurrentValue(ImageButton.ColumnIndexProperty, 0);
               break;
           case WpfImageButton.ImageLocation.Top:
               imageButton.SetCurrentValue(ImageButton.RowIndexProperty, 0);
               imageButton.SetCurrentValue(ImageButton.ColumnIndexProperty, 1);
               break;
           case WpfImageButton.ImageLocation.Right:
               imageButton.SetCurrentValue(ImageButton.RowIndexProperty, 1);
               imageButton.SetCurrentValue(ImageButton.ColumnIndexProperty, 2);
               break;
           case WpfImageButton.ImageLocation.Bottom:
               imageButton.SetCurrentValue(ImageButton.RowIndexProperty, 2);
               imageButton.SetCurrentValue(ImageButton.ColumnIndexProperty, 1);
               break;
           case WpfImageButton.ImageLocation.Center:
               imageButton.SetCurrentValue(ImageButton.RowIndexProperty, 1);
               imageButton.SetCurrentValue(ImageButton.ColumnIndexProperty, 1);
               break;
           default:
               throw new ArgumentOutOfRangeException();
       }
   }

   public ImageSource ImageSource
   {
       get { return (ImageSource)GetValue(ImageSourceProperty); }
       set { SetValue(ImageSourceProperty, value); }
   }

   public static readonly DependencyProperty ImageSourceProperty =
       DependencyProperty.Register("ImageSource", typeof(ImageSource), typeof(ImageButton), new PropertyMetadata(null));

   public int RowIndex
   {
       get { return (int)GetValue(RowIndexProperty); }
       set { SetValue(RowIndexProperty, value); }
   }

   public static readonly DependencyProperty RowIndexProperty =
       DependencyProperty.Register("RowIndex", typeof(int), typeof(ImageButton), new PropertyMetadata(0));

   public int ColumnIndex
   {
       get { return (int)GetValue(ColumnIndexProperty); }
       set { SetValue(ColumnIndexProperty, value); }
   }

   public static readonly DependencyProperty ColumnIndexProperty =
       DependencyProperty.Register("ColumnIndex", typeof(int), typeof(ImageButton), new PropertyMetadata(0));
}

public enum ImageLocation
{
   Left,
   Top,
   Right,
   Bottom,
   Center
}
```

3) Paste the following code into "Generic.xaml" file.

``` xml
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="clr-namespace:WpfImageButton">
    <Style TargetType="{x:Type local:ImageButton}" BasedOn="{StaticResource {x:Type Button}}">
        <Setter Property="ContentTemplate">
            <Setter.Value>
                <DataTemplate>
                    <Grid>
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto"/>
                            <RowDefinition Height="*"/>
                            <RowDefinition Height="Auto"/>
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto"/>
                            <ColumnDefinition Width="*"/>
                            <ColumnDefinition Width="Auto"/>
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageSource, RelativeSource={RelativeSource AncestorType=local:ImageButton}}"
                               Width="{Binding ImageWidth, RelativeSource={RelativeSource AncestorType=local:ImageButton}}"
                               Height="{Binding ImageHeight, RelativeSource={RelativeSource AncestorType=local:ImageButton}}"
                               Grid.Row="{Binding RowIndex, RelativeSource={RelativeSource AncestorType=local:ImageButton}}"
                               Grid.Column="{Binding ColumnIndex, RelativeSource={RelativeSource AncestorType=local:ImageButton}}"
                               VerticalAlignment="Center" HorizontalAlignment="Center"></Image>
                        <ContentPresenter Grid.Row="1" Grid.Column="1" Content="{TemplateBinding Content}"
                                          VerticalAlignment="Center" HorizontalAlignment="Center"></ContentPresenter>
                    </Grid>
                </DataTemplate>
            </Setter.Value>
        </Setter>
    </Style>
</ResourceDictionary>
```
