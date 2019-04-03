---
title: "JSON Web Token - exp, nbf, iat claims - convert DateTime to Unix time"
tags:
  - JWT
---

In JWT following claims must be a number containing a date as a number:
* `exp` (Expiration Time) Claim
* `nbf` (Not Before) Claim
* `iat` (Issued At) Claim

If you look into [IETF RFC7519](https://tools.ietf.org/html/rfc7519) you can find information about what exactly is this number:

>A JSON numeric value representing the number of seconds from 1970-01-01T00:00:00Z UTC until the specified UTC date/time, ignoring leap seconds.  This is equivalent to the IEEE Std 1003.1, 2013 Edition [POSIX.1] definition "Seconds Since the Epoch", in which each day is accounted for by exactly 86400 seconds, other than that non-integer values can be represented.  See RFC 3339      [RFC3339](https://tools.ietf.org/html/rfc3339) for details regarding date/times in general and UTC in particular.

If you develop your application with .NET Framework 4.6 or higher you can use [DateTimeOffset.ToUnixTimeSeconds](https://docs.microsoft.com/en-us/dotnet/api/system.datetimeoffset.tounixtimeseconds?view=netframework-4.6) method. But if your .NET Framework is lower you can create an extension method to convert `DateTime` to Unix timestamp. Below is simple implementation with such implementation:

~~~ csharp
public static class DateTimeExtensions
{
    public static long ToUnixTimestamp(this DateTime date)
    {
        var epoch = new DateTime(1970, 1, 1, 0, 0, 0, DateTimeKind.Utc);
        var time = date.ToUniversalTime().Subtract(epoch);
        return time.Ticks / TimeSpan.TicksPerSecond;
    }
}
~~~

If Unix timestamp is storing as a signed 32-bit binary integer in the year 2038 will be the problem known as ["Year 2038 problem"](https://en.wikipedia.org/wiki/Year_2038_problem).

*Resources:*

[https://tools.ietf.org/html/rfc7519](https://tools.ietf.org/html/rfc7519)
