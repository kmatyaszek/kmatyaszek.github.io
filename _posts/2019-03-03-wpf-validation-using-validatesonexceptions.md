---
title: "WPF Validation - Using ValidatesOnExceptions"
categories:
  - WPF Validation
tags:
  - WPF
  - C#
---
In WPF we have few options to validate user input data. In this article, I will show you validation based on the exception. This validation is applied in very simple solutions. The only thing you should do is throw an exception on property level and set `ValidatesOnExceptions` to true in the view.

We should consider two cases:
1. setter of the property throw exception and `ValidatesOnExceptions` property is set to true - in this case, when the user enters an invalid value a red border appears in the `TextBox` (and the `ToolTip` shows the error message - when we applied an appropriate style)
2. setter of the property throw exception and `ValidatesOnExceptions` property is set to false - in this case, property value does not change and there is not any information about invalid data in the view

In the below image you can see how looks invalid state of the `Name` property in the view:

<img src="/images/posts/WpfValidationException_2019-03-02_15-44-35.png" class="align-center" alt="">

So to activate this validation mechanism you should throw an exception in property setter:

~~~ csharp
public string Name
{
    get => _name;
    set
    {
        if (string.IsNullOrWhiteSpace(value))
            throw new Exception("Name cannot be empty!");

        _name = value;
        RaisePropertyChanged();
    }
}
~~~

And set `ValidatesOnExceptions` to true in XAML:
~~~ xml
<TextBox Text="{Binding Name, ValidatesOnExceptions=True,
                Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}" />
~~~

Full code example you can find on my GitHub account under the link:
[https://github.com/kmatyaszek/WPF-ValidatesOnExceptions](https://github.com/kmatyaszek/WPF-ValidatesOnExceptions)
