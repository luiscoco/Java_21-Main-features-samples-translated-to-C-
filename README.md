# Java_21 Main features translated to C#

## 1. Pattern Matching for switch

This feature extends the capabilities of switch expressions and statements, allowing you to express more complex and sophisticated data pattern matching

```csharp
interface Shape { }

// Classes with property syntax for getters
class Circle : Shape
{
    public double Radius { get; }

    public Circle(double radius)
    {
        Radius = radius;
    }
}

class Rectangle : Shape
{
    public double Width { get; }
    public double Height { get; }

    public Rectangle(double width, double height)
    {
        Width = width;
        Height = height;
    }
}

public class ShapeDescriber
{
    // describeShape method using pattern matching
    public static string DescribeShape(object shape)
    {
        return shape switch
        {
            Circle c => $"Circle with radius {c.Radius}",
            Rectangle r => $"Rectangle with width {r.Width} and height {r.Height}",
            null => "It's null",
            _ => "Some other shape"  // _ acts as the default case
        };
    }

    public static void Main(string[] args)
    {
        Circle circle = new Circle(5.0);
        Rectangle rectangle = new Rectangle(4.0, 6.0);

        Console.WriteLine(DescribeShape(circle));
        Console.WriteLine(DescribeShape(rectangle));
        Console.WriteLine(DescribeShape(null));
        Console.WriteLine(DescribeShape("String"));
    }
}
```

**Key Features Used**:

**Interfaces**: Interfaces remain very similar in C# and Java, providing blueprints for class behavior

**Classes with Property Syntax**: C# offers concise property syntax ({ get; }) for defining getters. This streamlines the getRadius, getWidth, and getHeight methods

**Pattern Matching with switch**: C# leverages pattern matching within switch expressions since C# 7. This eliminates the need for manual type checks and instanceof equivalents, giving your code a cleaner look, especially as shapes increase

**String Interpolation**: C# uses the $ prefix for string interpolation, a more readable way to combine variables and text within strings

Pattern matching in switch can be combined with 'guards' (additional boolean conditions) to refine matching criteria

```csharp
enum TrafficLight { RED, YELLOW, GREEN }

string HandleMessage(TrafficLight light)
{
    return light switch
    {
        TrafficLight.RED => "Stop!",
        TrafficLight.YELLOW => "Prepare to stop",
        TrafficLight.GREEN  => "Go!",
        _ => "Unknown signal" // Default case
    };
}
```

**Key Considerations**:

**Enums**: Enums in C# and Java are quite similar in their basic structure.

**Switch Expression**: Like Java, C# also supports switch expressions for cleaner pattern matching. In C#, you directly reference the enum values (TrafficLight.RED) without the class prefix.

**Default Case Importance**: Since enums have a well-defined set of values, a default case might seem unnecessary

However, potential changes to the enum or unexpected input would make a default case valuable for error handling.

How To Use It

```csharp
var currentLight = TrafficLight.YELLOW;
var message = HandleMessage(currentLight); 
Console.WriteLine(message); // Would print "Prepare to stop"
```

Pattern matching offers powerful data extraction mechanisms in switch expressions and statements

```csharp
// Record definitions for shapes
record Rectangle(int Width, int Height); 
record Circle(int Radius);
record Triangle(int Base, int Height);

string CalculateArea(Shape shape)
{
    return shape switch
    {
        Rectangle(int w, int h) => $"Rectangle area: {w * h}",
        Circle(int r)           => $"Circle area: {Math.PI * r * r}",
        Triangle(int b, int h)  => $"Triangle area: {0.5 * b * h}",
        _                       => "Unsupported shape" // Underscore for the default case 
    };
}
```

**Key Features**

**Records**: C# supports concise record types, which are perfect for representing data-centric structures like your shapes

Pattern Matching with Records: C# records integrate seamlessly with pattern matching in switch expressions

You can deconstruct the records into their properties (w, h, r, etc.) directly within the case patterns

**String Interpolation**: $ is used for clean string interpolation, combining calculations and text

**_ as default case**: The underscore (_) represents the default case, making the intent explicit


**Scenario: Parsing Geometric Shapes**

Suppose we have geometric shapes represented with a hierarchy of sealed classes and records:

```csharp
// Sealed interface using records for types
sealed interface Shape { } // No explicit 'permits' in C#
record Circle(double Radius) : Shape;
record Rectangle(double Width, double Height) : Shape;
record Triangle(double Base, double Height) : Shape;
record ComplexShape(List<Shape> SubShapes) : Shape;

// Utility class for clarity 
public static class ShapeUtils 
{
    public static double CalculateTotalArea(Shape shape)
    {
        return shape switch
        {
            Circle(double radius) => Math.PI * radius * radius,
            Rectangle(double width, double height) => width * height,
            Triangle(double base, double height) => 0.5 * base * height,
            ComplexShape(List<Shape> subShapes) =>
                subShapes.Select(ShapeUtils.CalculateTotalArea) // Recursive via Select
                         .Sum(),
            _ => throw new ArgumentException("Unknown shape type") // Updated exception type
        };
    }
}
```

**Key Considerations**:

**Sealed Interfaces**: C# lacks a direct permits keyword. A sealed interface implicitly controls allowed implementations. However, this doesn't enforce compile-time checking like permits

**Records**: C# records work just like in previous examples

**Recursion with LINQ**: Instead of Java's stream(), we use LINQ methods. Select maps elements to calculated areas, and Sum calculates the aggregate

**Exception Handling**: C# uses ArgumentException where Java might use IllegalArgumentException

The core function call is found within the recursive calculation for a **ComplexShape**:

```csharp
subShapes.Select(ShapeUtils.CalculateTotalArea).Sum()
```

## 2. Record Patterns

Record patterns streamline working with record types, providing concise syntax for deconstructing them

```csharp
record Point(int X, int Y);

void MovePoint(object obj)
{
    if (obj is Point(int x, int y)) // Pattern matching in the 'if' condition
    {
        // x and y are in scope here
        Console.WriteLine($"Coordinates: ({x}, {y})");
    }
}
```

**Key Considerations**:

**Records**: C# record syntax and behavior are very similar to Java's

Pattern Matching in if Statements: C# allows pattern matching directly within if conditions

If obj matches the Point record pattern, the properties x and y are automatically deconstructed and available within the if block

String Interpolation: **C# uses $ for string interpolation**

Important Note: **Object Equality**

In Java, instanceof  checks for a type match and potential nullity

For a closer translation of this behavior in  C#, you would slightly modify the condition:

```csharp
if (obj is Point { } point)  // Note the additional '{' and '}' 
{
    var (x, y) = point;  // Deconstruction into separate variables
    Console.WriteLine($"Coordinates: ({x}, {y})");
}
```

This modified if handles null cases and adds an extra layer of deconstruction into named variables (x and y)

**Record patterns can be nested** to extract data from more complex record hierarchies:

```csharp
record Customer(string Name, Address Address);
record Address(string Street, string City, string ZipCode);

void ProcessCustomer(Customer customer)
{
    if (customer is Customer(string name, Address(_, string city, _)) && city == "New York") 
    {
        Console.WriteLine($"Customer {name} is from New York.");
    }
}
```

**Explanations**:

**Records**: C# records continue to mirror Java's record structure

**Nested Pattern Matching**: The if condition demonstrates nested pattern matching. We are interested in the Customer record's properties (name and Address) as well as specifically in the city property within the Address

**Underscore (_) for Ignored Values**: The underscores (_) act as placeholders for properties within the Address that we don't need to use

**Combined Condition**: C#'s pattern matching allows us to merge nested pattern checks with additional conditions into a single if statement. We check for the pattern and the city being "New York" simultaneously

The statement customer is Customer(string name, Address(_, string city, _)) && city == "New York" does the following:

**Pattern Matching**: Checks if customer matches the structure of a Customer with nested Address. Deconstructs it if a match is found

**City Extraction**: Only checks those matching customers having 'city' within their address data

**City Comparison**: Additionally requires that city is equal to "New York"

Let's dive into more advanced and **complex scenarios** demonstrating the power of Record Patterns

**Scenario 1: Deeply Nested Data Structures**

Imagine we're working with a user information structure nested several levels deep:

```csharp
record Address(string Street, string City, string ZipCode);
record Customer(string Name, Address Address);
record Order(int OrderId, Customer Customer, List<LineItem> Items);
record LineItem(string Product, int Quantity);

// Task-like behavior simulation 
void ProcessOrders(List<Order> orders)
{
    var citiesWithLargeOrders = orders.Where(order => LargeOrder(order))
                                      .SelectMany(order => ExtractCities(order))
                                      .ToHashSet(); // Equivalent to Collectors.toSet()

    Console.WriteLine(string.Join(", ", citiesWithLargeOrders)); 
}

private bool LargeOrder(Order order)
{
    return order.Items.Select(item => item.Quantity).Sum() > 100; 
}

private IEnumerable<string> ExtractCities(Order order)
{
    if (order is Order(_, Customer(_, Address(_, string city, _)), _))
    {
        return new List<string> { city };
    }
    else
    {
        return Enumerable.Empty<string>(); // Like List.of() for an empty list
    }
}
```


Absolutely! Let's translate your Java code to use C# 12-style features. Here's the converted version along with explanations:

```csharp
record Address(string Street, string City, string ZipCode);
record Customer(string Name, Address Address);
record Order(int OrderId, Customer Customer, List<LineItem> Items);
record LineItem(string Product, int Quantity);

// Task-like behavior simulation 
void ProcessOrders(List<Order> orders)
{
    var citiesWithLargeOrders = orders.Where(order => LargeOrder(order))
                                      .SelectMany(order => ExtractCities(order))
                                      .ToHashSet(); // Equivalent to Collectors.toSet()

    Console.WriteLine(string.Join(", ", citiesWithLargeOrders)); 
}

private bool LargeOrder(Order order)
{
    return order.Items.Select(item => item.Quantity).Sum() > 100; 
}

private IEnumerable<string> ExtractCities(Order order)
{
    if (order is Order(_, Customer(_, Address(_, string city, _)), _))
    {
        return new List<string> { city };
    }
    else
    {
        return Enumerable.Empty<string>(); // Like List.of() for an empty list
    }
}
```

**Key Considerations**:

**Records**: C# records remain nearly identical to their Java counterparts.

**LINQ for Stream-like Operations**: C# uses LINQ methods (Where, SelectMany, ToHashSet) to replicate the Java stream() pipeline.

**Pattern Matching in Methods**: ExtractCities leverages pattern matching with records just like it would in a regular if condition.

**Enumerable.Empty**: Equivalent to Java's List.of() for an empty sequence.

**Task Simulation**: While C# has true asynchronous support (async/await), this simplified version mimics the flow of your Task-based concept.

Let's Break it Down

ProcessOrders:

Where acts as filter, keeping only large orders.

SelectMany works like flatMap, allowing extraction of potentially multiple cities per order.

ToHashSet is the C# equivalent of Collectors.toSet().

string.Join(", ", citiesWithLargeOrders):  This neatly formats the output for printing.




