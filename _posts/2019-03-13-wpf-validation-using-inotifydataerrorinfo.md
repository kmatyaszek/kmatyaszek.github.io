---
title: "WPF Validation - Using INotifyDataErrorInfo"
categories:
  - WPF Validation
tags:
  - WPF
  - C#
---
In the .NET 4.5 was introduced new interface `INotifyDataErrorInfo` which enables data entity classes to implement custom validation rules and expose validation results asynchronously. This interface has three members:
* `HasErrors` property indicates whether there are any validation errors
* `GetErrors` method returns an `IEnumerable` that contains validation errors for the specified property (when the propertyName parameter isn't equal to null or empty string) or for the entire entity (when the propertyName parameter is equal to null or empty string)
* `ErrorsChanged` event must occur when the validation errors have changed for a property or for the entity

As `GetErrors` returns `IEnumerable` you can return multiple errors per property. Also, you can return custom error object (this is not possible using `IDataErrorInfo` interface). As you can see this new interface provides more flexible validation model in WPF. By default, `ValidatesOnNotifyDataErrors` is set to true so you don't have to set this explicit in the binding expression.
Below you can see the result of my example program (as you can see `TextBox` has two errors).
<img src="/images/posts/WPFValidationINotifyDataErrorInfo_2019-03-13_10-54-16.png" class="align-center" alt="">

Below you can see an example implementation of the `INotifyDataErrorInfo`:
~~~ csharp
public class MainViewModel : BindableBase, INotifyDataErrorInfo
{
    private string _userName;
    private readonly Dictionary<string, List<string>> _errorsByPropertyName = new Dictionary<string, List<string>>();

    public MainViewModel()
    {
        UserName = null;
    }

    public string UserName
    {
        get => _userName;
        set
        {
            _userName = value;
            ValidateUserName();
            RaisePropertyChanged();
        }
    }

    public bool HasErrors => _errorsByPropertyName.Any();

    public event EventHandler<DataErrorsChangedEventArgs> ErrorsChanged;

    public IEnumerable GetErrors(string propertyName)
    {
        return _errorsByPropertyName.ContainsKey(propertyName) ?
            _errorsByPropertyName[propertyName] : null;
    }

    private void OnErrorsChanged(string propertyName)
    {
        ErrorsChanged?.Invoke(this, new DataErrorsChangedEventArgs(propertyName));
    }

    private void ValidateUserName()
    {
        ClearErrors(nameof(UserName));
        if (string.IsNullOrWhiteSpace(UserName))
            AddError(nameof(UserName), "Username cannot be empty.");
        if (string.Equals(UserName, "Admin", StringComparison.OrdinalIgnoreCase))
            AddError(nameof(UserName), "Admin is not valid username.");
        if (UserName == null || UserName?.Length <= 5)
            AddError(nameof(UserName), "Username must be at least 6 characters long.");
    }

    private void AddError(string propertyName, string error)
    {
        if (!_errorsByPropertyName.ContainsKey(propertyName))
            _errorsByPropertyName[propertyName] = new List<string>();

        if (!_errorsByPropertyName[propertyName].Contains(error))
        {
            _errorsByPropertyName[propertyName].Add(error);
            OnErrorsChanged(propertyName);
        }
    }

    private void ClearErrors(string propertyName)
    {
        if (_errorsByPropertyName.ContainsKey(propertyName))
        {
            _errorsByPropertyName.Remove(propertyName);
            OnErrorsChanged(propertyName);
        }
    }
}
~~~

And below snippet of code shows you how you can show to user multiple errors to single property:
{% highlight xml %}
<TextBox Text="{Binding UserName, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged, ValidatesOnNotifyDataErrors=True}">
    <Validation.ErrorTemplate>
        <ControlTemplate>
            <StackPanel>
                <AdornedElementPlaceholder x:Name="textBox" />
                <ItemsControl ItemsSource="{Binding}">
                    <ItemsControl.ItemTemplate>
                        <DataTemplate>
                            <TextBlock Text="{Binding ErrorContent}" Foreground="Red" />
                        </DataTemplate>
                    </ItemsControl.ItemTemplate>
                </ItemsControl>
            </StackPanel>
        </ControlTemplate>
    </Validation.ErrorTemplate>
</TextBox>
{% endhighlight %}
A complete example is available on the following link: [https://github.com/kmatyaszek/WPFValidationINotifyDataErrorInfo](https://github.com/kmatyaszek/WPFValidationINotifyDataErrorInfo)
