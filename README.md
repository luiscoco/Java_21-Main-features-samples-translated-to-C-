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


