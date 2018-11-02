@title[SmartEnum - A better C# enum for DDD]

## SmartEnum<br>
### A better C# enum for DDD 

**Antão Almada**<br>
Principal Engineer<br>
Farfetch - Store of the Future<br>

---

# `Enum`

+++

### `Enum` implicit definition

```csharp
public enum MyEnum
{
    First,
    Second,
    Third
}
```

+++

### `Enum` explicit definition

```csharp
public enum MyEnum : short
{
    First = 1,
    Second = 2,
    Third = 3
}
```

@[1] (Inner type definition. (default is `int`))
@[3-5] (Explicit value assignment. (default is incremental, starting at 0))

+++

### `Enum` as value aliasing

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

@[5,6] (Multiple aliases for the same value are allowed.)
@[3,7] (Same alias for different values is now allowed.)

+++

### Switching an `Enum`

```csharp
public enum MyEnum : short
{
    First = 1,
    Second = 2,
    Third = 3
}

public static class MyClass
{
    public static int DoSomething(MyEnum value)
    {
        switch (value)
        {
            case MyEnum.First:
                return 1;
            case MyEnum.Second:
                return 2;
            case MyEnum.Third:
                return 3;
            default:
                throw new Exception("Unexpected value!");
        }
    }
}
```

@[11-21] ()
@[1-6] (Three aliases defined!)
@[14-19] (Three aliases handled!)
@[20-21] (Why a `default` clause?)

+++

### Unexpected values?

@ul

- A new alias was added and someone forgot to update this method.
- C# compiler allows invalid values! @fa[frown-o]

@ulend

+++

### Unexpected values (explicit cast)

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

### Unexpected values (default constructor)

```csharp
public enum MyEnum : short
{
    First = 1,
    Second = 2,
    Third = 3
}

var values = new MyEnum[10];
```

@[8] (`enum` is a value-type and the default parameterless constructor sets value to 0.)

@snap[south-west] [Live code](https://sharplab.io/#v2:C4LgTgrgdgPgAgBgARwIwG4CwAoHcDMSAplBALZICyAngKKkUhIDOAFgPZjA4DeOAkADEAlmGbAkAXiSoANDiSKkAZSIBjdlAAmUpACZ52JUgAqrUTun5DAXxx5CaAGxIADgEMuw9wBsUeqjoGWgAPYBJmYU1mXgUlAhRUFzRkAHEiYAA5dzIiAApgc2ZA+nIkADdfCCIASiQ4xT4jYyVmAHdhYDVWJDzKn2qahpamlrGkNXdmIhKGADoRMVB65vHxuAB2JCgconYAMzyaUrIF0XEarFW1iamZ4/nVDW0mYZv4rZ3cg6Og8jmnpotJc3uNJtNZv8zBZXtd3opNttdj8HlDzGBgVd4UgtER9u4ID5QKD3oUwOw2tsiJTQmoiK5gFEoHkAEQAVSgRBCrnU4R0/WqAEIWSC4Uo7NcJTYlPZsAlnP5EltYtd5UkUAAWKjuYTMobXUbjSpgCpVIjFaScymosgAbVQCAAuljxvtOER3N1esbTQMZrrfdVmPq1vw0ABOPpmubpLK7PI1UXGKVAA) @snapend

---

# `SmartEnum`

+++

### `SmartEnum`

@ul

- A fix for the unexpected values.
- Cleaner code.
- Not a new concept (many article found online).
- Surprisingly, very few implementations found:
  - [Ardalis.SmartEnum](https://github.com/ardalis/SmartEnum)
  - [Constant](https://github.com/projecteon/Constant)
  - [Farfetch.Framework.SmartEnum](https://gitlab.fftech.info/antao.almada/framework-smart-enum)

@ulend

+++

### `SmartEnum` definition

```csharp
public sealed class MySmartEnum : SmartEnum<MySmartEnum>
{
    public static readonly MySmartEnum First =
        new MySmartEnum(nameof(First), 1);
    public static readonly MySmartEnum Second =
        new MySmartEnum(nameof(Second), 2);
    public static readonly MySmartEnum Third =
        new MySmartEnum(nameof(Third), 3);

    private MySmartEnum(string name, int value)
        : base(name, value) {}
}
```

@[1] (A reference-type that derives from `SmartEnum<TEnum>`.)
@[3-8] (Aliases defined as `public static readonly` fields.)
@[10] (Constructor set to `private` so that no more instances can be explicitly created.)
@[1] (Class set to `sealed` so that no instances can be created by derived class constructor.)

+++

### `SmartEnum` explicit definition

```csharp
public sealed class MySmartEnum : SmartEnum<MySmartEnum, short>
{
    public static readonly MySmartEnum First =
        new MySmartEnum("A string.", 1);
    public static readonly MySmartEnum Second =
        new MySmartEnum("Another string.", 2);
    public static readonly MySmartEnum Third =
        new MySmartEnum("Yet another string.", 3);

    private MySmartEnum(string name, short value)
        : base(name, value) {}
}
```

@[1, 10] (The inner value type can be defined. (default is `int`))
@[4,6,8] (Spaces are allowed in strings.)

+++

### `SmartEnum` as value aliasing

```csharp
public sealed class MySmartEnum : SmartEnum<MySmartEnum>
{
    public static readonly MySmartEnum First =
        new MySmartEnum(nameof(First), 1);
    public static readonly MySmartEnum Second =
        new MySmartEnum(nameof(Second), 2);
    public static readonly MySmartEnum Third =
        new MySmartEnum(nameof(Third), 3);
    public static readonly MySmartEnum AnotherThird =
        new MySmartEnum(nameof(AnotherThird), 3);
    public static readonly MySmartEnum First =
        new MySmartEnum(nameof(First), 3); // runtime error

    private MySmartEnum(string name, int value)
        : base(name, value) {}
}
```

@[7-8,9-10] (Multiple aliases for the same value are allowed.)
@[3-4,11-12] (Same alias for different values is not allowed but now it's a runtime error. A Roslyn analyzer can help with this.)

+++

### `SmartEnum` equality comparison

```csharp
MySmartEnum.First.Equals(MySmartEnum.First); // = true
MySmartEnum.First.Equals(MySmartEnum.Third); // = false
MySmartEnum.Third.Equals(MySmartEnum.AnotherThird); // = true
```

@[1-3] (Same behavior as `enum`. **NOTE:** *Ardalis* has a different behavior!)

+++

### Mean elapsed time (ns)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["GetHashCode", "Equals_Null", "Equals_SameValue", "Equals_DifferentValue", "Equals_DifferentType", "EqualOperator", "NotEqualOperator"], 
        "datasets" : [
            {
                "data":[0.0016, 0, 14.0599, 13.8287, 11.3394, 0.0006, 0.0029],
                "label":"enum",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[9.0170, 2.3111, 2.3230, 15.3502, 7.1682, 9.2394, 10.1589],
                "label":"Constant",
                "backgroundColor":"rgba(73,159,104,1)"
            },
            {
                "data":[26.8611, 2.6078, 2.1612, 11.2824, 7.6414, 10.0053, 11.1820],
                "label":"Ardalis",
                "backgroundColor":"rgba(119,178,140,1)"
            },
            {
                "data":[1.2080, 1.4195, 1.9837, 2.6676, 6.4906, 3.4570, 3.5695],
                "label":"Farfetch",
                "backgroundColor":"rgba(194,197,187,1)"
            }
        ]
    }
}
-->
</canvas>

+++

### Allocated (B)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["GetHashCode", "Equals_Null", "Equals_SameValue", "Equals_DifferentValue", "Equals_DifferentType", "EqualOperator", "NotEqualOperator"], 
        "datasets" : [
            {
                "data":[0, 0, 48, 48, 48, 0, 0],
                "label":"enum",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[0, 0, 0, 0, 0, 0, 0],
                "label":"Constant",
                "backgroundColor":"rgba(73,159,104,1)"
            },
            {
                "data":[32, 0, 0, 0, 0, 0, 0],
                "label":"Ardalis",
                "backgroundColor":"rgba(119,178,140,1)"
            },
            {
                "data":[0, 0, 0, 0, 0, 0, 0],
                "label":"Farfetch",
                "backgroundColor":"rgba(194,197,187,1)"
            }
        ]
    }
}
-->
</canvas>

---

## Handling `SmartEnum`

+++

### Switching `SmartEnum`

```csharp
public static int DoSomething(MySmartEnum value)
{
    if (value is null)
        throw new ArgumentNullException(nameof(value));

    switch (value.Name)
    {
        case nameof(MySmartEnum.First):
            return 1;
        case nameof(MySmartEnum.Second):
            return 2;
        case nameof(MySmartEnum.Third):
            return 3;
        default:
            throw new Exception("Unexpected value!");
    }
}
```

@[3-4] (`SmartEnum` is a value-type so it can be `null`. This will better handled using C# 8.0 "nullable reference types".)
@[6,8-13] (Uses name `string` comparison.)
@[14-15] (`default` clause still required for when someone forgets to add new value clauses.)

+++

### Switching `SmartEnum` using pattern matching

```csharp
public static int DoSomething(MySmartEnum value)
{
    switch(value)
    {
        case null:
            throw new ArgumentNullException(nameof(value));
        case var e when e.Equals(MySmartEnum.First):
            return 1;
        case var e when e.Equals(MySmartEnum.Second):
            return 2;
        case var e when e.Equals(MySmartEnum.Third):
            return 3;
        default:
            throw new Exception("Unexpected value!");
    }
}
```

@[5-6] (Still a `null` check...)
@[3,7-12] (Uses equality comparison.)
@[13-14] (Still a `default` clause...)

+++

### How to better handle unexpected values?

@ul

- Less invalid states => Less validations => Less code to maintain!
- Consider using *F#* instead!
  - ["Domain Modeling Made Functional"](https://pragprog.com/book/swdddf/domain-modeling-made-functional) by Scott Wlaschin
- `SmartEnum` is a class, use inheritance!

@ulend

+++

### Adding behavior to a `SmartEnum`

```csharp
public abstract class EmployeeType : SmartEnum<EmployeeType>
{
    public static readonly EmployeeType Manager =
        new ManagerType();
    public static readonly EmployeeType Assistant =
        new AssistantType();

    protected EmployeeType(string name, int value)
        : base(name, value) {}

    public abstract decimal BonusSize { get; }

    sealed class ManagerType : EmployeeType
    {
        public ManagerType() : base("Manager", 1) {}

        public override decimal BonusSize => 10_000m;
    }

    sealed class AssistantType : EmployeeType
    {
        public AssistantType() : base("Assistant", 2) {}

        public override decimal BonusSize => 1_000m;
    }
}
```

@[11] (An `abstract` property.)
@[13-25] (`private sealed` classes `override` the property behavior.)
@[3-6] (The `public static readonly` fields are defined by instances of the derived classes.)

+++

### Defining a state machine

```csharp
public abstract class ReservationStatus : SmartEnum<ReservationStatus>
{
    public static readonly ReservationStatus New =
        new NewStatus();
    public static readonly ReservationStatus Accepted =
        new AcceptedStatus();
    public static readonly ReservationStatus Paid =
        new PaidStatus();
    public static readonly ReservationStatus Cancelled =
        new CancelledStatus();

    protected ReservationStatus(string name, int value)
        : base(name, value) {}

    public abstract bool CanTransitionTo(ReservationStatus next);

    sealed class NewStatus: ReservationStatus
    {
        public NewStatus() : base("New", 0) {}

        public override bool CanTransitionTo(ReservationStatus next) =>
            next == ReservationStatus.Accepted ||
            next == ReservationStatus.Cancelled;
    }

    sealed class AcceptedStatus: ReservationStatus
    {
        public AcceptedStatus() : base("Accepted", 1) {}

        public override bool CanTransitionTo(ReservationStatus next) =>
            next == ReservationStatus.Paid ||
            next == ReservationStatus.Cancelled;
    }

    sealed class PaidStatus: ReservationStatus
    {
        public PaidStatus() : base("Paid", 2) {}

        public override bool CanTransitionTo(ReservationStatus next) =>
            next == ReservationStatus.Cancelled;
    }

    sealed class CancelledStatus: ReservationStatus
    {
        public CancelledStatus() : base("Cancelled", 3) {}

        public override bool CanTransitionTo(ReservationStatus next) =>
            false;
    }
}
```

@[15] (The `abstract` `CanTransitionTo()` method returns `true` is allowed to transition from current state to another; otherwise `false`.)
@[3-10] (All possible states.)
@[17, 21-23] (`New` can transition to `Accepted` and `Cancelled`.)
@[26, 30-32] (`Accepted` can transition to `Paid` and `Cancelled`.)
@[35, 39-40] (`Paid` can transition to `Cancelled`.)
@[43, 47-48] (`Cancelled` cannot transition to any other state.)

---

## Serialization

+++

### Entity serialization

@ul

- Domain entities should be serializable.
- Serialization libraries know how to handle `enum` but not `SmartEnum`.
- Serialization libraries create new instances but `SmartEnum` values are singletons.

@ulend

+++

### `SmartEnum` serialization support

@ul

- Libraries supported:
  - [Json.NET](https://www.newtonsoft.com/json)
  - [Utf8Json](https://github.com/neuecc/Utf8Json)
  - [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)
  - [protobuf-net](https://github.com/mgravell/protobuf-net)
- No dependencies added to main package.
- All serialization packages are optional.  

@ulend

+++

### Json.NET
#### Mean elapsed time (ns)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["Serialize_Name", "Serialize_Value", "Deserialize_Name", "Deserialize_Value"], 
        "datasets" : [
            {
                "data":[992.05, 566.43, 1407.09, 1311.58],
                "label":"enum",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[712.34, 768.42, 913.36, 943.95],
                "label":"SmartEnum",
                "backgroundColor":"rgba(73,159,104,1)"
            }
        ]
    }
}
-->
</canvas>

+++

### Json.NET
#### Allocated (B)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["Serialize_Name", "Serialize_Value", "Deserialize_Name", "Deserialize_Value"], 
        "datasets" : [
            {
                "data":[1624, 1272, 2936, 2680],
                "label":"enum",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[1600, 1520, 2888, 2904],
                "label":"SmartEnum",
                "backgroundColor":"rgba(73,159,104,1)"
            }
        ]
    }
}
-->
</canvas>

+++

### Utf8Json
#### Mean elapsed time (ns)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["Serialize_Name", "Serialize_Value", "Deserialize_Name", "Deserialize_Value"], 
        "datasets" : [
            {
                "data":[95.47, 70.55, 232.96, 176.59],
                "label":"enum",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[81.66, 66.85, 282.00, 195.31],
                "label":"SmartEnum",
                "backgroundColor":"rgba(73,159,104,1)"
            }
        ]
    }
}
-->
</canvas>

+++

### Utf8Json
#### Allocated (B)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["Serialize_Name", "Serialize_Value", "Deserialize_Name", "Deserialize_Value"], 
        "datasets" : [
            {
                "data":[40.0, 40.0, 64.0, 64.0],
                "label":"enum",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[40.0, 40.0, 96.0, 64.0],
                "label":"SmartEnum",
                "backgroundColor":"rgba(73,159,104,1)"
            }
        ]
    }
}
-->
</canvas>

+++

### MessagePack-CSharp
#### Mean elapsed time (ns)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["Serialize_Name", "Serialize_Value", "Deserialize_Name", "Deserialize_Value"], 
        "datasets" : [
            {
                "data":[87.39, 57.37, 95.40, 39.08],
                "label":"enum",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[78.12, 51.51, 98.59, 58.34],
                "label":"SmartEnum",
                "backgroundColor":"rgba(73,159,104,1)"
            }
        ]
    }
}
-->
</canvas>

+++

### MessagePack-CSharp
#### Allocated (B)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["Serialize_Name", "Serialize_Value", "Deserialize_Name", "Deserialize_Value"], 
        "datasets" : [
            {
                "data":[32, 32, 56, 24],
                "label":"enum",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[32, 56, 96, 24],
                "label":"SmartEnum",
                "backgroundColor":"rgba(73,159,104,1)"
            }
        ]
    }
}
-->
</canvas>

+++

### protobuf-net
#### Mean elapsed time (ns)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["Serialize_Name", "Serialize_Value", "Deserialize_Name", "Deserialize_Value"], 
        "datasets" : [
            {
                "data":[0, 498.42, 0, 633.80],
                "label":"enum",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[566.92, 517.62, 1348.35, 966.51],
                "label":"SmartEnum",
                "backgroundColor":"rgba(73,159,104,1)"
            }
        ]
    }
}
-->
</canvas>

+++

### protobuf-net
#### Allocated (B)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["Serialize_Name", "Serialize_Value", "Deserialize_Name", "Deserialize_Value"], 
        "datasets" : [
            {
                "data":[0, 208, 0, 80],
                "label":"enum",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[232, 232, 352, 104],
                "label":"SmartEnum",
                "backgroundColor":"rgba(73,159,104,1)"
            }
        ]
    }
}
-->
</canvas>

+++

### SmartEnum
#### Mean elapsed time (ns)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["Serialize_Name", "Serialize_Value", "Deserialize_Name", "Deserialize_Value"], 
        "datasets" : [
            {
                "data":[712.34, 768.42, 913.36, 943.95],
                "label":"Json.NET",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[81.66, 66.85, 282.00, 195.31],
                "label":"Utf8Json",
                "backgroundColor":"rgba(73,159,104,1)"
            },
            {
                "data":[78.12, 51.51, 98.59, 58.34],
                "label":"MessagePack-CSharp",
                "backgroundColor":"rgba(119,178,140,1)"
            },
            {
                "data":[566.92, 517.62, 1348.35, 966.51],
                "label":"protobuf-net",
                "backgroundColor":"rgba(194,197,187,1)"
            }
        ]
    }
}
-->
</canvas>

+++

### SmartEnum
#### Allocated (B)

<canvas class="stretch" data-chart="bar">
<!-- 
{ 
    "data" : {
        "labels" : ["Serialize_Name", "Serialize_Value", "Deserialize_Name", "Deserialize_Value"], 
        "datasets" : [
            {
                "data":[1600, 1520, 2888, 2904],
                "label":"Json.NET",
                "backgroundColor":"rgba(21,122,110,1)"
            },
            {
                "data":[40, 40, 96, 64],
                "label":"Utf8Json",
                "backgroundColor":"rgba(73,159,104,1)"
            },
            {
                "data":[32, 56, 96, 24],
                "label":"MessagePack-CSharp",
                "backgroundColor":"rgba(119,178,140,1)"
            },
            {
                "data":[232, 232, 352, 104],
                "label":"protobuf-net",
                "backgroundColor":"rgba(194,197,187,1)"
            }
        ]
    }
}
-->
</canvas>

---

### Final notes

@ul

- `SmartEnum` instances are singletons and references are the size of a pointer, whatever inner type used...
- `SmartEnum` is a reference-type so its passed-by-reference.

@ulend

---

### Conclusions

@ul

- `SmartEnum` cannot beat `enum` on raw computation performance.
- `SmartEnum` has equivalent performance to `enum` when modeling a domain.
- `SmartEnum` allows easier to maintain code.
- Check repository at https://gitlab.fftech.info/antao.almada/framework-smart-enum

@ulend

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