# C#

- [Difference between `ref` and `out` keywords](#difference-between-`ref`-and-`out`-keywords)
- [LINQ syntax](#linq-syntax)
- [Delegates](#delegates)
- [`Tuple` and `ValueTuple`](#`tuple`-and-`valueTuple`)
## Difference between `ref` and `out` keywords
 Parameter with `ref` keyword does not require initialization. Parameter with `out` keyword does.

## LINQ syntax
 There are two types of syntax for LINQ.
 **1. Query Syntax**
 ``` csharp
 IEnumerable<Type> results = from x in MyListOfThings
                where x.CheckSomething()
                orderby x.SomeAttribute // or orderby x
                select x;
```
The `MyListOfThings` variable can be an `IEnumerable` or an `IQueryable`.<br/><br/>
**2. Method Syntax**
``` csharp
IEnumerable<Type> results = MyListOfThings.Where(x => x.CheckSomething()).Orderby(x => x.SomeAttribute);
// or Orderby(x => x)
```
## Delegates
General syntax:
``` csharp
// Instantiation
public delegate Type MyDelMethod(parameters);
// Method description
public static Type MyMethod1(parameters) { /* Method Description */ }
// Method calling
MyDelMethod m1 = MyMethod1;
m1(someParameter);
```
**Note:** Delegate methods are objects, so they can be passed as parameters of other methods, or assigned as properties of other objects.<br/>
Example:<br/>
**1. As parameters of other methods**
``` csharp
public delegate int MyMathDelegate(params int[] numbers);
public static int MySumMethod(params int[] numbers)
{
    int sum = 0;
    int max = numbers.Length;
    for(int i = 0; i < max; i++) sum += numbers[i];
    return sum;
}
public static void PrintResult(params int[] list, MyMathDelegate m)
    => Console.WriteLine("The result is {0}", m(list));
```
Then, in the `Main` method:
``` csharp
MyMathDelegate newMath = MySumMethod;
PrintResult(1, 2, 3, 4, newMath);
```
This would print in the console: `The result is 10`.<br/><br/>
**2. As properties of other objects**<br/>
Continuing from the previous example, let us assume a class that would only contain methods (that is not static, for the purpose of this example):
``` cs
public class MyMathClass
{
    public int Sum(params int[] numbers);
    public int Mean(params int[] numbers);
    public int StdDev(params int[] numbers);
}
```
Now, in the `Main` method:
```cs
MyMathClass inst = new MyMathClass();
MyMathDelegate GetSum = inst.Sum;
MyMathDelegate GetMean = inst.Mean;
MyMathDelegate GetSD = inst.StdDev;
// Now, here comes a handy trick
MyMathDelegate GetEverything = GetSum + GetMean + GetSD;
```
Yep, that's a sum of methods! When `GetEverything` is invoked, all those delegate methods are called in that order.

## `Tuple` and `ValueTuple`
`System.Tuple` is a struct, which means it is value type (and mutable too), whereas `System.ValueTuple` is a class, aka reference type.<br/>
**`Tuple` Example:**
``` cs
public Tuple<int, int> GetSumMean(params int[] numbers)
{
    int sum = 0, avg = 0, max = numbers.Length;
    for(int i = 0; i < max; i++) sum += numbers[i];
    avg = sum / max;
    return new Tuple(sum, avg);
}
```
In the `Main` method:
``` cs
Tuple<int, int> values = GetSumMean(1, 2, 3, 4, 5);
```
**`ValueTuple` Example:**
``` cs
public (int s, int m) GetSumMean(params int[] numbers)
{
    var ret = (s: 0, m: 0)
    int sum = 0, avg = 0, max = numbers.Length;
    for(int i = 0; i < max; i++) ret.s += numbers[i];
    ret.m = sum / max;
    return ret;
}
```
In the `Main` method:
``` cs
var (a, b) = GetSumMean(1, 2, 3, 4, 5); // a = 15, b = 3
```