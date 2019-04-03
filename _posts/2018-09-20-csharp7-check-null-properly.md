---
title: "C# 7: check null properly"
tags:
  - C#
---
If you develop application with C# 7 and you want to be sure that null is always check properly you should use constant pattern "null". This is one of the 3 types of patterns in the new feature in C# called "pattern matching". In this case, you don't have to bother if in the class is defined override of the equals operator. In the following code snippets, we will go through two types of checking null (is and ==) and with or without override == operator.

Code Snippet #1 present empty class Foo with checking null using constant pattern "null". In the IL we can see that to test if foo is null is used `brtrue.s`. `brtrue.s` instruction transfers control to the specific target (in our case to `IL_000d`).

~~~
// ------------------------------- Snippet #1 ---------------------------------
C# code:
class Program
{
    static void Main(string[] args)
    {
        Foo foo = null;
        if (foo is null)
            Console.WriteLine("foo is null");
        Console.ReadLine();
    }
    class Foo  { }
}

IL code:
.method private hidebysig static void  Main(string[] args) cil managed
{
  .entrypoint
  // Code size       20 (0x14)
  .maxstack  8
  IL_0000:  ldnull
  IL_0001:  brtrue.s   IL_000d
  IL_0003:  ldstr      "foo is null"
  IL_0008:  call       void [mscorlib]System.Console::WriteLine(string)
  IL_000d:  call       string [mscorlib]System.Console::ReadLine()
  IL_0012:  pop
  IL_0013:  ret
} // end of method Program::Main
~~~

Code Snippet #2 present empty class Foo with checking null using == operator. IL is exactly the same as in the Snippet #1.
~~~
// ------------------------------- Snippet #2 ---------------------------------
C# code:
class Program
{
    static void Main(string[] args)
    {
        Foo foo = null;
        if (foo == null)
            Console.WriteLine("foo is null");
        Console.ReadLine();
    }
    class Foo { }
}

IL code:
.method private hidebysig static void  Main(string[] args) cil managed
{
  .entrypoint
  // Code size       20 (0x14)
  .maxstack  8
  IL_0000:  ldnull
  IL_0001:  brtrue.s   IL_000d
  IL_0003:  ldstr      "foo is null"
  IL_0008:  call       void [mscorlib]System.Console::WriteLine(string)
  IL_000d:  call       string [mscorlib]System.Console::ReadLine()
  IL_0012:  pop
  IL_0013:  ret
} // end of method Program::Main
~~~

Code Snippet #3 present class Foo with a user-defined override of the equals operator with checking null using constant pattern "null". In this case, even if we have a user-defined override in the IL code is generated `brtrue.s` instruction.
~~~
// ------------------------------- Snippet #3 ---------------------------------
C# code:
class Program
{
    static void Main(string[] args)
    {
        Foo foo = null;
        if (foo is null)
            Console.WriteLine("foo is null");
        Console.ReadLine();
    }

    class Foo
    {
        public int Id { get; set; }

        public static bool operator ==(Foo x, Foo y)
        {
            if (!ReferenceEquals(x, null) && !ReferenceEquals(y, null))
                return x.Id == y.Id;

            return ReferenceEquals(x, null) && ReferenceEquals(y, null);
        }

        public static bool operator !=(Foo x, Foo y)
        {
            return !(x == y);
        }

        public override bool Equals(object obj)
        {
            Foo r = obj as Foo;
            if (r != null)
            {
                return r == this;
            }
            return false;
        }

        public override int GetHashCode()
        {
            return Id.GetHashCode();
        }
    }
}

IL code:
.method private hidebysig static void  Main(string[] args) cil managed
{
  .entrypoint
  // Code size       20 (0x14)
  .maxstack  8
  IL_0000:  ldnull
  IL_0001:  brtrue.s   IL_000d
  IL_0003:  ldstr      "foo is null"
  IL_0008:  call       void [mscorlib]System.Console::WriteLine(string)
  IL_000d:  call       string [mscorlib]System.Console::ReadLine()
  IL_0012:  pop
  IL_0013:  ret
} // end of method Program::Main
~~~

Code Snippet #4 present class Foo with a user-defined override of the equals operator with checking null using == operator. In this case in the IL code is used binary operator `op_Equality`.

~~~
// ------------------------------- Snippet #4 ---------------------------------
C# code:
class Program
{
    static void Main(string[] args)
    {
        Foo foo = null;
        if (foo == null)
            Console.WriteLine("foo is null");
        Console.ReadLine();
    }

    class Foo
    {
        public int Id { get; set; }

        public static bool operator ==(Foo x, Foo y)
        {
            if (!ReferenceEquals(x, null) && !ReferenceEquals(y, null))
                return x.Id == y.Id;

            return ReferenceEquals(x, null) && ReferenceEquals(y, null);
        }

        public static bool operator !=(Foo x, Foo y)
        {
            return !(x == y);
        }

        public override bool Equals(object obj)
        {
            Foo r = obj as Foo;
            if (r != null)
            {
                return r == this;
            }
            return false;
        }

        public override int GetHashCode()
        {
            return Id.GetHashCode();
        }
    }
}


IL code:
.method private hidebysig static void  Main(string[] args) cil managed
{
  .entrypoint
  // Code size       26 (0x1a)
  .maxstack  8
  IL_0000:  ldnull
  IL_0001:  ldnull
  IL_0002:  call       bool BlogIsNull.Program/Foo::op_Equality(class BlogIsNull.Program/Foo,
                                                                class BlogIsNull.Program/Foo)
  IL_0007:  brfalse.s  IL_0013
  IL_0009:  ldstr      "foo is null"
  IL_000e:  call       void [mscorlib]System.Console::WriteLine(string)
  IL_0013:  call       string [mscorlib]System.Console::ReadLine()
  IL_0018:  pop
  IL_0019:  ret
} // end of method Program::Main
~~~

As you can see in Snippet #4 you can't be 100% sure that checking null by using == operator will be properly implemented because it's using user-defined override. So you have to use constant pattern "null" in this case.

*References:*

[https://www.ecma-international.org/publications/standards/Ecma-335.htm](https://www.ecma-international.org/publications/standards/Ecma-335.htm)
