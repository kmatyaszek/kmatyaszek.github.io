---
title: "Json.NET - ignoring properties during serialization"
tags:
  - Json.NET
  - C#
---

When you're using *Json.NET* to serialize an object by default all public fields and properties will be serialized. In this case, if you want to ignore some of them you can use `JsonIgnore` attribute. In the following example, you can see how you can ignore `SSN` property during the serialization process.

~~~ csharp
public class Worker
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Surname { get; set; }
    [JsonIgnore]
    public string SSN { get; set; }
    public DateTime DayOfBirth;
}
~~~

And below is the result of this serialization:

~~~ json
{
  "DayOfBirth": "1970-03-10T00:00:00",
  "Id": "b9d2fae0-91e9-4890-afba-b289907b1cb6",
  "Name": "John",
  "Surname": "Smith"
}
~~~

If you want to have more control over what is serialized you can change the default behavior of Json.NET by specifying `JsonObject` attribute with property `MemberSerialization` set to `OptIn`. In this situation, only members marked with `JsonProperty` attribute or `DataMember` attribute will be serialized. If you set `MemberSerialization` to `OptIn` and you don't specify any member to serialize (attributes `JsonProperty` or `DataMember`) you will receive empty JSON object - `{}`.
The result of the following code is exactly the same as in the first example with ignoring `SSN` property and leaving the default value of the `MemberSerialization` property set to `OptOut`.

~~~ csharp
[JsonObject(MemberSerialization.OptIn)]
public class Worker
{
    [JsonProperty]
    public Guid Id { get; set; }
    [JsonProperty]
    public string Name { get; set; }
    [JsonProperty]
    public string Surname { get; set; }        
    public string SSN { get; set; }
    [JsonProperty]
    public DateTime DayOfBirth;
}
~~~
