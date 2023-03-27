# Record Type
>The `record` keyword to define a reference type that provides built-in functionality for encapsulating data.
>C# 10 allows the `record class` syntax as a synonym to clarify a reference type, and `record struct` to define a value type with similar functionality. 

## The following two examples demonstrate `record` (or `record class`) reference types with _immutable_ properties by using positional parameters or standard property syntax:

```csharp
// NOTE: Positional parameters (FirstName, LastName) start with uppercase
public record Person(string FirstName, string LastName);

public record Person
{
    public string FirstName { get; init; } = default!;
    public string LastName { get; init; } = default!;
};
```
## The following two examples show `record struct` value types with _immutable_ properties and fields:

```csharp
public readonly record struct Point(double X, double Y, double Z);

public record struct Point
{
    public double X {  get; init; }
    public double Y {  get; init; }
    public double Z {  get; init; }
};
```
## The following example shows `record` and `record struct` with _mutable_ properties and fields:

```csharp
// Mutable because of setter
public record Person
{
    public string FirstName { get; set; } = default!;
    public string LastName { get; set; } = default!;
};

// Mutable because readonly is missing
public record struct DataMeasurement(DateTime TakenAt, double Measurement);

public record struct Point
{
    public double X { get; set; }
    public double Y { get; set; }
    public double Z { get; set; }
}
```

## Built-in behavior:
* Value-based equality
* GetHashCode
* Copy & Clone
* PrintMembers & ToString
* Deconstruct

---
## Special notes:
* For record struct types, a parameterless constructor that sets each field to its default value.
* A `Deconstruct` method with an `out` parameter for each positional parameter provided in the record declaration. The method deconstructs properties defined by using positional syntax; it ignores properties that are defined by using standard property syntax.
* Immutability can be useful when you need a data-centric type to be thread-safe or you're depending on a hash code remaining the same in a hash table. Immutability isn't appropriate for all data scenarios, however. Entity Framework Core, for example, doesn't support updating with immutable entity types.
* `record` class and `readonly record struct` have _shallow immutability_. It means you can't change the value of value-type properties or the reference of reference-type properties. However, the data that a reference-type property refers to can be changed.
```csharp
public record Person(string FirstName, string LastName, string[] PhoneNumbers);
Person person = new("Mohammed", "Hoque", new string[1] { "214-1234" });
person.PhoneNumbers[0] = "469-1234"; // Update phone nuumber
Console.WriteLine(person.PhoneNumbers[0]); // output: 469-1234
``` 
---
## Value equality
* For `class` types, two objects are equal if they refer to the same object in memory.
* For `struct` types, two objects are equal if they are of the same type and store the same values.
* For `record` types, including `record struct` and `readonly record struct`, two objects are equal if they are of the same type and store the same values.
---
## Nondestructive mutation
* A `with` expression makes a new record instance that is a copy of an existing record instance, with specified properties and fields modified.
```csharp
public record Person(string FirstName, string LastName);
Person person1 = new("Mohammed", "Hoque");
// output: Person { FirstName = Mohammed, LastName = Hoque}

Person person2 = person1 with { LastName = "XYZ" };
// output: Person { FirstName = Mohammed, LastName = XYZ}
```

---
## Inheritance (only for `record class` types)
* A record can inherit from another record. However, a record can't inherit from a class, and a class can't inherit from a record.

```csharp
public abstract record Person(string FirstName, string LastName);
public record Friend(string FirstName, string LastName, string EmailAddress): Person(FirstName, LastName)
```
---
## Examples:

```csharp
public record Person(string FirstName, string LastName);

public static void Main()
{
    Person person = new("Mohammed", "Hoque");
    Console.WriteLine(person);
    // output: Person { FirstName = Mohammed, LastName = Hoque }
}

/* The person type is a positional record containing the
   properties for the first and last name. Those properties
   map to the JSON elements "firstName" and "lastName" when
   serialized or deserialized. */
public record Person([property: JsonPropertyName("firstName")]string FirstName, 
    [property: JsonPropertyName("lastName")]string LastName);

/*
    The following example declares the FirstName and LastName
    properties of a positional record "public", but restricts the 
    Id positional parameter to "internal"
*/
public record Person(string FirstName, string LastName, string Id)
{
    internal string Id { get; init; } = Id;
}
```

---
## Links
* [Records (C# reference)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/record)
