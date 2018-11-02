@title[Mastering Enumeration in .NET]

## SmartEnum<br>
### A better C# enum for DDD 

**Antão Almada**<br>
Principal Engineer<br>
Farfetch - Store of the Future<br>

---

# `Enum`

+++

## `Enum` implicit definition

```csharp
public enum MyEnum
{
    First,
    Second,
    Third
}
```

+++

## `Enum` explicit definition

```csharp
public enum MyEnum : short
{
    First = 1,
    Second = 2,
    Third = 3
}
```

@[1] (Inner type definition (default is `int`))
@[3-5] (Explicit value assignment (default is incremental, starting at 0))

+++

## `Enum` as value aliasing

```csharp
public enum MyEnum : short
{
    First = 1,
    Second = 2,
    Third = 3,
    AnotherThird = 3,
    First = 3 // compilation error
}
```

@[5,6] (Multiple aliases for the same value are allowed)
@[3,7] (Same alias for different value is now allowed)

+++

## Switching an `Enum`

```csharp
public enum MyEnum : short
{
    First = 1,
    Second = 2,
    Third = 3
}

public static partial class MyEnumExtensions
{
    public static string GetName(this MyEnum value)
    {
        switch (value)
        {
            case MyEnum.First:
                return nameof(MyEnum.First);
            case MyEnum.Second:
                return nameof(MyEnum.Second);
            case MyEnum.Third:
                return nameof(MyEnum.Third);
            default:
                throw new Exception("Unexpected value!");
        }
    }
}
```

@[20-21] (Why a `default` clause?)

+++

## Unexpected values?

- A new value was added and someone forgot to update this method.
- C# compiler allows invalid values!

+++

## Unexpected values (explicit cast)

```csharp
public enum MyEnum : short
{
    First = 1,
    Second = 2,
    Third = 3
}

var value = (MyEnum)0;
```

@[8] (This is valid C#)

+++

## Unexpected values (default constructor)

```csharp
public enum MyEnum : short
{
    First = 1,
    Second = 2,
    Third = 3
}

var values = new MyEnum[10];
```

@[8] (`enum` is a value-type and the default parameterless constructor sets value to 0)

@snap[south-west] [Live code](https://sharplab.io/#v2:C4LgTgrgdgPgAgBgARwIwG4CwAoHcDMSAplBALZICyAngKKkUhIDOAFgPZjA4DeOAkADEAlmGbAkAXiSoANDiSKkAZSIBjdlAAmUpACZ52JUgAqrUTun5DAXxx5CaAGxIADgEMuw9wBsUeqjoGWgAPYBJmYU1mXgUlAhRUFzRkAHEiYAA5dzIiAApgc2ZA+nIkADdfCCIASiQ4xT4jYyVmAHdhYDVWJDzKn2qahpamlrGkNXdmIhKGADoRMVB65vHxuAB2JCgconYAMzyaUrIF0XEarFW1iamZ4/nVDW0mYZv4rZ3cg6Og8jmnpotJc3uNJtNZv8zBZXtd3opNttdj8HlDzGBgVd4UgtER9u4ID5QKD3oUwOw2tsiJTQmoiK5gFEoHkAEQAVSgRBCrnU4R0/WqAEIWSC4Uo7NcJTYlPZsAlnP5EltYtd5UkUAAWKjuYTMobXUbjSpgCpVIjFaScymosgAbVQCAAuljxvtOER3N1esbTQMZrrfdVmPq1vw0ABOPpmubpLK7PI1UXGKVAA) @snapend

---

<table>
	<tr>
		<th>@fa[twitter]</th>
		<th>[@AntaoAlmada](https://twitter.com/AntaoAlmada) </th>
	</tr>
	<tr>
		<th>@fa[medium]</th>
		<th>[@antao.almada](https://medium.com/@antao.almada)</th>
	</tr>
	<tr>
		<th>@fa[github]</th>
		<th>[aalmada](https://github.com/aalmada) </th>
	</tr>
</table>