---
title: "WPF Validation - Display errors to the user"
categories:
  - WPF Validation
tags:
  - WPF
  - C#
---
In this blog post, I will show you how you can validate data using `IDataErrorInfo`. This interface has only two members which need to be implemented:
* `public string Error { get; }` - gets an error message indicating what is wrong with this object
* `public string this[string columnName] { get; }` - get the error message for the property with the given name.

Once you implement the `IDataErrorInfo` you need to set the `ValidatesOnDataErrors` to true in the element that you want to validate.
To simplify the code example, I implemented `IDataErrorInfo` in the `MainViewModel` as you can see below:

~~~ csharp
public class MainViewModel : BindableBase, IDataErrorInfo
{
    private string _firstName;
    private string _lastName;

    public string FirstName
    {
        get { return _firstName; }
        set { _firstName = value; RaisePropertyChanged(); }
    }              

    public string LastName
    {
        get { return _lastName; }
        set { _lastName = value; RaisePropertyChanged(); }
    }

    public string this[string columnName]
    {
        get
        {
            string error = string.Empty;

            switch (columnName)
            {
                case nameof(FirstName):
                    if (string.IsNullOrWhiteSpace(FirstName))
                        error = "First name cannot be empty.";
                    if (FirstName?.Length > 50)
                        error = "The name must be less than 50 characters.";
                    break;

                case nameof(LastName):
                    if (string.IsNullOrWhiteSpace(LastName))
                        error = "Last name cannot be empty.";
                    break;
            }

            return error;
        }
    }

    public string Error => string.Empty;
}
~~~

and in the `TextBox` element, I set `ValidatesOnDataErrors` to `true`:

~~~ xml
<TextBox Text="{Binding FirstName, Mode=TwoWay,
            UpdateSourceTrigger=PropertyChanged, ValidatesOnDataErrors=True}" />
~~~

 If you want to have only information about which property is invalid you don't have to implement `Error` property (as I have done in my example). Full example with the `IDataErrorInfo` implementation you can find under the link: [https://github.com/kmatyaszek/WPFValidationIDataErrorInfo](https://github.com/kmatyaszek/WPFValidationIDataErrorInfo )
