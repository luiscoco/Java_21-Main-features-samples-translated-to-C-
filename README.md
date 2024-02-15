# Java 21 Main features translated to C#

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

**Key Considerations**:

**Records**: C# records remain nearly identical to their Java counterparts

**LINQ for Stream-like Operations**: C# uses LINQ methods (Where, SelectMany, ToHashSet) to replicate the Java stream() pipeline

**Pattern Matching in Methods**: ExtractCities leverages pattern matching with records just like it would in a regular if condition

**Enumerable.Empty**: Equivalent to Java's List.of() for an empty sequence

**Scenario 2: Polymorphism & Record Patterns**

Consider a scenario where we have an abstract base class, with derived classes implemented as records:

```csharp
using System;

// API Response definitions (from previous example)
abstract class ApiResponse { }
record SuccessResponse(string Message, object Data) : ApiResponse;
record ErrorResponse(int Code, string Message) : ApiResponse;

// Method for response handling
async void HandleApiResponse(ApiResponse response) 
{
    if (response is SuccessResponse(string msg, object data))
    {
        Console.WriteLine($"Success: {msg}");

        // Type checking and processing (several approaches possible)
        switch (data) 
        {
            case string strData:
                Console.WriteLine($"Data (string): {strData}");
                break;
            case int intData:
                Console.WriteLine($"Data (int): {intData}");
                break;
            // Add more cases as needed...
        }
    }
    else if (response is ErrorResponse(int code, string errMsg))
    {
        Console.WriteLine($"Error ({code}): {errMsg}");
    }
}

// Main class for our console application
class Program 
{
    static void Main(string[] args)
    {
        // Sample API responses
        var successResponse = new SuccessResponse("Operation completed", 42);
        var errorResponse = new ErrorResponse(500, "Internal server error");

        // Handling responses
        HandleApiResponse(successResponse);
        HandleApiResponse(errorResponse);

        // Keep the console open 
        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
}
```

## 3. String Templates

String templates enhance Java's string **formatting** capabilities, enabling you to embed expressions directly within strings:

```csharp
int width = 5;
int height = 8;
string message = $"""
    Shape dimensions:
    Width:  {width}
    Height: {height}
""";
Console.WriteLine(message);
```

**Explanation of Changes**:

**String Interpolation**: C# uses $ for interpolated strings. Everything enclosed in curly braces ({}) will have its value inserted directly into the string. This is cleaner than Java's .formatted method.

**Triple-Quotes for Multiline Strings**:  Using triple double-quotes (**"""**) in C# allows you to create multiline strings directly within the code, removing the need for  string concatenation in multiline formatting.

**Console.WriteLine**:  This replaces **System.out.println** for outputting the message to the console.

String templates can handle **rich formatting**, including number formatting and alignment:

```csharp
double price = 129.95;
string formatted = $"""
        Invoice
        Item: Widget XYZ
        Price: ${price:C2} 
        """;
Console.WriteLine(formatted);
```

**Explanation of Changes**:

**String Interpolation**: Like before, we use C#'s $ for string interpolation, which eliminates the need for the separate .formatted call.

**Currency Formatting (C2)**: The :C2 format specifier is added to directly format the price variable as currency with two decimal places.

This C# code will produce an output similar to this:

```
Invoice
Item: Widget XYZ
Price: $129.95 
```

**String Templates**

```csharp
int age = 35;
string name = "Alice";
double temperature = 23.6;

string formattedMessage = $@"Hello, my name is {name} and I am {age} years old. 
                             The current temperature is {temperature} degrees Celsius.";

Console.WriteLine(formattedMessage);
```

This is the output for the above code

```
Hello, my name is Alice and I am 35 years old. 
The current temperature is 23.6 degrees Celsius.
```

**Key Changes**:

**Verbatim String with @**: C# uses the @ symbol before the opening quote to create verbatim strings

Verbatim strings treat most special characters (like newline characters) literally, making it better for multiline formatting without explicit escape sequences

**String Interpolation**: Similar to previous examples, we use $ within the verbatim string to embed the values of name, age, and temperature

**Sequenced Collections**

Sequenced Collections provide greater control over the processing of collections in a sequential manner. Let's combine this with string formatting:

```csharp
using System.Linq;
using System.Collections.Generic;

List<string> fruits = new List<string> { "Apple", "Banana", "Mango", "Orange" };

string formattedList = string.Join(", ", fruits.Select(fruit => fruit.ToUpper()));
formattedList = $"[{formattedList}]";

Console.WriteLine(formattedList);
```

**Explanation of Changes**:

**LINQ**: C# uses LINQ (Language-Integrated Query) for operations similar to Java Streams. It provides methods like Select (equivalent to Java's map) for transformations.

**string.Join**: This method is used to join the transformed elements of the list into a comma-separated string.

**String Interpolation**:  C#'s string interpolation ($) makes it easy to directly embed the formatted list within the square brackets.

**Outpu**

The code will produce the following output:

```
[APPLE, BANANA, MANGO, ORANGE]
```

**More complex string formatting**

```csharp
// Your Product class (you'll need to define this)
class Product {
    public string Name { get; set; } 
    public double Price { get; set; }
    public int Quantity { get; set; }

    // ... (Constructor)
}

// ... 
using System.Linq;
using System.Collections.Generic;

List<Product> order = new List<Product>();
order.Add(new Product("Laptop", 999.99, 2));
order.Add(new Product("Keyboard", 45.50, 1));

string report = $@"--- Order Summary ---
{string.Join("", order.Select(p => string.Format("{0,-20} ${1,8:F2} x{2}\n", p.Name, p.Price, p.Quantity)))}";

Console.WriteLine(report);
```

**Explanation of Changes**:

**C# Properties**: The name, price, and quantity fields are replaced by C# properties (Name, Price, Quantity)

**LINQ Formatting**: The core formatting logic from the stream operation is preserved. C#'s string.Format is used similarly to Java's String.format, with minor syntax differences in the format specifiers

**String Interpolation**: We use $ again for embedding the order summary inside the report string. string.Join concatenates the formatted order lines

**Output**

The code will produce an output similar to this:

```
--- Order Summary ---
Laptop               $ 999.99 x2
Keyboard             $  45.50 x1
```

## 4. Scoped Values

Scoped values introduce a safer way to use resources that need to be automatically closed

Scoped values ensure correct closure, similar to try-with-resources but more versatile

While we don't have an exact equivalent of Java's scoped values in C#, we can replicate the core functionality using the **using** statement and the **IDisposable** interface. Let's break down how:

```csharp
// Our C# equivalent of your Java 'AutoCloseableFile'
class AutoCloseableFile : IDisposable
{
    private string _filename;

    public AutoCloseableFile(string filename)
    {
        _filename = filename;
        // Simulated file opening logic in the real implementation
        Console.WriteLine("File opened: " + filename);
    }

    public string ReadContent()
    {
        // Simulated file reading in the real implementation
        return "Sample content from " + _filename; 
    }

    public void Dispose()
    {
        // Simulated file closing logic in the real implementation
        Console.WriteLine("File closed: " + _filename);
    }
}
```

C# Translation using **using**:

```csharp
using (var file = new AutoCloseableFile("data.txt"))
{
    string content = file.ReadContent();
    // ... do something with content
} // 'file' is automatically closed (and Dispose() is called) here
```

**Explanation**:

**IDisposable**: The AutoCloseableFile class implements the IDisposable interface. This interface ensures that the Dispose method is available to handle resource cleanup (such as closing files)

**using** Statement:  C#'s using statement guarantees that the Dispose method will be called on the file object, even if exceptions occur, providing the same resource safety as Java's try-with-resources

**Important Notes**:

In a real-world implementation, the AutoCloseableFile class would handle actual file opening, reading, and closing operations

C#'s using statement provides a concise and reliable way to manage resources that need cleanup

Scoped values go beyond standard try-with-resources to enable more flexible resource handling

```csharp
using System.IO;

void WriteToFileAndDatabase(string data)
{
    using (var fileWriter = new StreamWriter("log.txt")) // StreamWriter for better text handling
    {
        // Database Connection Handling (Needs Specific Implementation)
        using (var dbConnection = database.GetConnection()) 
        {
            // Assuming there's a database object and a GetConnection() method  
            
            fileWriter.Write(data);

            // Database Execution (Needs Specific Implementation)
            var command = dbConnection.CreateCommand();
            command.CommandText = "INSERT INTO logs VALUES (@data)";
            command.Parameters.AddWithValue("@data", data);
            command.ExecuteNonQuery();
        }
    }
}
```

Let's delve into additional examples demonstrating the capabilities of Scoped Values

**Scenario 1: Implicit Database Connection Scope**

Let's simulate a scenario where we establish a database connection and make it implicitly available to various data access methods

```csharp
using System;

// C# equivalent of your simulated database connection class
public class DatabaseConnection : IDisposable
{ 
    public DatabaseConnection()
    {
        Console.WriteLine("Opening database connection");
    }

    // Placeholder for simulating a database query execution
    public void ExecuteQuery(string query)
    {
        Console.WriteLine("Executing query: " + query);
    }

    // Implements the IDisposable interface
    public void Dispose()
    {
        Console.WriteLine("Closing database connection");
    }
}
```

**Explanation**:

**IDisposable Interfac**e: The class implements IDisposable to define a standard mechanism for releasing resources (in this case, simulating the closing of a database connection).

**Constructor and Methods**: These remain largely the same, simulating the opening of a connection and execution of a query.

**Resource Handling**: While simplified here, using a using statement with this class in C# will ensure that the Dispose method is called, just like how a try-with-resources statement works in Java.

**Important Considerations**:

**Real Implementation**: In a real-world scenario, you would use an actual database library (like System.Data.SqlClient for SQL Server) and provide implementations that open, query, and close database connections according to that library's conventions.

**Error Handling**: It's essential to handle potential exceptions (SQLException in the Java version, likely specific database exceptions in the C# version) for robust database interaction.

**Scenario 2: Hierarchical Request Data**

We can use Scoped Values to model **nested request** information or hierarchical contexts

C# doesn't have a direct counterpart to Java's hypothetical ScopedData pattern, let's break down how you can achieve similar functionality using standard C# constructs:

Simulating Scoped Data with **Closures/Lambdas**

```csharp
User user = new User("Alice");
HttpRequest request = new HttpRequest("10.0.0.1");

// Using a lambda to capture 'user' in the current scope
Action action = () => HandleNestedRequest(user, request); 
action(); 

static void HandleNestedRequest(User user, HttpRequest request)
{
    Console.WriteLine("User: " + user.name);
    Console.WriteLine("Request IP: " + request.ipAddress);
    // ... other nested processing
}
```

**Explanation**:

**Lambda/Closure**: The lambda expression **() => HandleNestedRequest(user, request)** captures the user and request variables from the outer scope

When action() is executed, it accesses these captured values within HandleNestedRequest

**Trade-offs**: Unlike Java's ScopedValue, this doesn't provide the same explicit lifespan management or chained execution with .thenCombine

You need to call the action (**action()**) to simulate the **scoped execution**

## 5. Structured Concurrency

Structured concurrency simplifies the management of multiple threads

Instead of dealing with threads directly, it treats multiple tasks as a single unit of work for improved reliability and error handling

```csharp
using System;
using System.Threading.Tasks;

try 
{
    Task<string> task1 = Task.Run(() => "Result of Task 1");
    Task<int> task2 = Task.Run(() => 42);

    // Wait for tasks to complete (if necessary)
    Console.WriteLine(task1.Result); 
    Console.WriteLine(task2.Result);
} 
catch (AggregateException ex)
{
    // Handle potential exceptions from tasks
    foreach (var innerEx in ex.InnerExceptions)
    {
        Console.WriteLine(innerEx.Message);
    }
}
```

**Explanation of Changes**:

**Task.Run**: Task.Run is used to start new tasks that run asynchronously. It automatically leverages the .NET thread pool to manage threads, similar to the concept of virtual threads in Java

**Lambdas**: C# utilizes lambda expressions (() => ...) to define the work each task performs

**try-catch**: A try-catch block is included to handle potential exceptions thrown by the tasks. If an exception occurs in one of the tasks, it will be wrapped in an AggregateException

**Result Property**: Accessing the Result property of a Task will block the current thread until the task completes and returns the calculated result

**Important Notes**:

**Virtual Threads**: While .NET automatically manages the thread pool and may optimize thread usage, C# doesn't expose the explicit 'virtual threads' concept in the same way Java does

**Exception Handling**: Task-based parallelism provides built-in mechanisms for handling exceptions that occur within asynchronous operations

**Output**:

The code will produce the following output:

```
Result of Task 1
42
```

**Structured concurrency offers elegant error handling for concurrent tasks**

```csharp
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

try 
{
    List<Task<string>> tasks = new List<Task<string>>
    {
        Task.Run(() => PossiblyFailingTask1()),
        Task.Run(() => PossiblyFailingTask2())
    };

    // Wait for all tasks to complete
    Task.WaitAll(tasks.ToArray());

    // Process results, potentially handle exceptions
    foreach (var task in tasks)
    {
        try
        {
            Console.WriteLine(task.Result);
        }
        catch (AggregateException ex)  // Handles exceptions from tasks
        {
            foreach (var innerEx in ex.InnerExceptions)
            {
                Console.WriteLine("Task failed: " + innerEx.Message);
            }
        }
    }
}
catch (AggregateException ex)
{
    // Handle potential exceptions with any of the tasks themselves
    foreach (var innerEx in ex.InnerExceptions)
    {
        Console.WriteLine(innerEx.Message);
    }
}

// ... Your hypothetical 'PossiblyFailingTask1' and 'PossiblyFailingTask2' methods
string PossiblyFailingTask1() { /* ... */ throw new Exception("Task 1 error"); } 
string PossiblyFailingTask2() { /* ... */ return "Task 2 Success"; } 
```

**Explanation of Changes**:

**Task Creation**: Tasks are created using Task.Run to encapsulate PossiblyFailingTask1 and PossiblyFailingTask2

**invokeAll Simulation**: While there's no invokeAll equivalent, storing tasks in a list and using Task.WaitAll achieves the same effect of waiting for all tasks to complete

**Exception Handling**: C#'s AggregateException is used to catch potential exceptions from tasks. Inner exceptions provide individual failure reasons

**Hypothetical Tasks**: You'll need to implement your PossiblyFailingTask1 and PossiblyFailingTask2 methods and replace the placeholders in this example

**Key Points**:

**Virtual Threads**: Similar to the previous example, C#'s .NET thread pool manages task execution efficiently

**Synchronization**: Task.WaitAll blocks the thread until all tasks in the array have completed

Structured concurrency radically simplifies working with multiple concurrent tasks within a single unit

**Coordinating and Handling Errors Among Tasks**:

C# doesn't have a built-in framework that precisely replicates Java's StructuredTaskScope

However, we can combine C# concepts to achieve a similar structured execution pattern with controlled error propagation. Here's how you might approach this translation:

```csharp
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

try
{
    List<Task> tasks = new List<Task>();

    // Task 1: Network Operation
    tasks.Add(Task.Run(() => PerformNetworkOperation()));

    // Task 2: Expensive Computation
    tasks.Add(Task.Run(() => ComputeExpensiveValue()));

    // Wait for all tasks to complete
    Task.WaitAll(tasks.ToArray());

    // Check for Exceptions
    List<Exception> exceptions = new List<Exception>();
    foreach (var task in tasks)
    {
        if (task.IsFaulted) 
        {
            exceptions.AddRange(task.Exception.InnerExceptions);
        }
    }

    if (exceptions.Count > 0)
    {
        throw new AggregateException(exceptions); // Propagate exceptions
    }

    // Retrieve Results 
    Console.WriteLine("Network result: " + tasks[0].Result);
    Console.WriteLine("Computed value: " + tasks[1].Result);
}
catch (AggregateException ex)
{
    foreach (var innerEx in ex.InnerExceptions)
    {
        Console.WriteLine("Task failed: " + innerEx.Message);
    }
}

// Implement your methods (return types need to match Task results)
string PerformNetworkOperation() { /* ... */ } 
int ComputeExpensiveValue() { /* ... */ } 
```

**Explanation**:

**Task Management**: Task.Run starts asynchronous tasks, and we store them in a list for tracking

**Synchronization**: Task.WaitAll provides the equivalent of scope.join()

**Error Handling**: The IsFaulted property on each Task indicates if an exception occurred 

An AggregateException is used to bundle and propagate multiple exceptions, similar to Java's behavior 

**resultNow Alternative**: Since we wait for tasks to complete, accessing the Result property directly is safe. This avoids potential blocking that resultNow could cause

**Limitations**:

No Automatic 'Shutdown on Failure': C# doesn't have a built-in equivalent to immediately fail sibling tasks if one fails. You would need to add manual cancellation logic if this is a strict requirement.

**Virtual threads** introduce lightweight threading for massively scalable applications

**Handling Many Concurrent Clients**:

```csharp
using System.Net;
using System.Net.Sockets;
using System.Text; 
using System.Threading.Tasks;

try
{
    var serverSocket = new TcpListener(IPAddress.Any, 8080);
    serverSocket.Start(); 

    while (true)
    {
        var clientSocket = await serverSocket.AcceptTcpClientAsync();

        // Start a task to handle the client request concurrently
        _ = Task.Run(() => HandleClientRequest(clientSocket)); 
    }
}
catch (SocketException ex)
{
    Console.WriteLine("Socket Error: " + ex.Message);
}

async void HandleClientRequest(TcpClient clientSocket) 
{
    try 
    {
        using (clientSocket)
        using (var networkStream = clientSocket.GetStream())
        {
            // Read request (assuming a simple text-based protocol for this example)
            byte[] buffer = new byte[1024];
            int bytesRead = await networkStream.ReadAsync(buffer, 0, buffer.Length);
            string request = Encoding.UTF8.GetString(buffer, 0, bytesRead); 

            // Process request...
            string response = "Here's a response from the server!"; 

            // Send response...
            byte[] responseBytes = Encoding.UTF8.GetBytes(response);
            await networkStream.WriteAsync(responseBytes, 0, responseBytes.Length);
        } // clientSocket and networkStream disposed automatically here
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error handling client request: " + ex.Message);
    }
}
```

## 6. Similar capabilities as SequencedCollection in C#

While we don't have the identical **SequencedCollection** interface in C# 12,  let's explore how you can achieve similar functionality and design patterns with C#'s **built-in collections** and **LINQ**

**Collections with Sequencing Abilities**

**LinkedList<T>**: Best for scenarios heavily focused on adding and removing elements from the beginning or end. Efficient AddFirst, AddLast, RemoveFirst, and RemoveLast operations

Accessing the first or last elements is done through First and Last properties

**List<T>**: While primarily geared towards random access, you can simulate some 'sequenced' behaviors

Adding to the start or end is less efficient (Insert(0, ...) or Add(...))

Getting the first or last element can be done with indexing (list[0] or list[list.Count - 1])

**LINQ Methods for Sequencing**

**First()/FirstOrDefault()**: Retrieves the first element, or a default value if the collection is empty

**Last()/LastOrDefault()**: Retrieves the last element, or a default value if the collection is empty

**Reverse()**: Returns an IEnumerable<T> with the elements in reverse order

**Example Scenarios**

### 1. Queue-like Behavior

```csharp
LinkedList<string> taskQueue = new LinkedList<string>();
taskQueue.AddLast("Process Data");
taskQueue.AddLast("Send Report");

string nextTask = taskQueue.First.Value; 
taskQueue.RemoveFirst(); 
```

### 2.  Getting Latest Items

```csharp
List<int> sensorReadings = new List<int> { 15, 22, 18, 25 };
int latestReading = sensorReadings.Last();
```

### 3.  Processing in Reverse

```csharp
string[] names = { "Alice", "Bob", "Charlie" };
foreach (string name in names.Reverse()) 
{
    Console.WriteLine(name);
}
```

**Important Considerations**:

**Efficiency**: Choose LinkedList<T> if frequent first/last manipulations are essential. Otherwise, List<T> with LINQ suffices for many scenarios

**Immutability**: LINQ methods often return new sequences (like Reverse()). If you need to modify the original collection, you'll need additional manipulations

**Note**: If you often need a data structure with both stack-like and queue-like behavior, consider C#'s Queue<T> and Stack<T>  classes

**LinkedList<T>** in C# provides inherent '**first**' and '**last**' node access:

```csharp
using System.Collections.Generic;

public class SequencedCollectionDemo
{
    public static void Main(string[] args)
    {
        LinkedList<string> myList = new LinkedList<string>();
        myList.AddLast("Alice");
        myList.AddLast("Bob");
        myList.AddLast("Charlie");

        myList.AddFirst("Zara");
        myList.AddLast("Eve");

        Console.WriteLine("First: " + myList.First.Value);
        Console.WriteLine("Last: " + myList.Last.Value);

        myList.RemoveFirst();
        myList.RemoveLast();

        Console.WriteLine("Modified List: " + string.Join(", ", myList)); 
    }
}
```

**Pros**:

Efficient AddFirst, AddLast, RemoveFirst, RemoveLast operations

**Cons**:

Slower random access (e.g., getting by index) compared to List<T>

While C# lacks **built-in** equivalents for **LinkedHashSet** and the reversed method from a  **SequencedCollection**, let's see how to accomplish similar behavior:

Using **LinkedHashSet<T>** (Closest Equivalent)

C# has **LinkedHashSet<T>** which preserves insertion order, just like Java's version:

```csharp
using System.Collections.Generic;

public class SequencedSetDemo
{
    public static void Main(string[] args)
    {
        LinkedHashSet<int> mySet = new LinkedHashSet<int>();
        mySet.Add(5);
        mySet.Add(1);
        mySet.Add(3);

        // Getting the First Element (Iterator needed)
        int firstElement = mySet.First(); 
        Console.WriteLine("First element: " + firstElement);

        // Reversing Order
        List<int> reversedList = new List<int>(mySet);
        reversedList.Reverse();

        Console.WriteLine("Reversed set: " + string.Join(", ", reversedList));
    }
}
```

**Explanation**:

**LinkedHashSet<T>** Maintains the order elements were added, giving you the 'sequenced' aspect

**Getting the First Element**: .NET's **LinkedHashSet<T>** doesn't have getFirst

The easiest way is to access it via the set's enumerator using First()

Reversing: While there's no built-in reversed method, you can convert the set to a List<T>, reverse it, and display the reversed list

**Important Notes**:

**Iteration vs. Direct Access**: **LinkedHashSet<T>** is designed for maintaining order during iteration

Getting the 'first' element directly requires using the enumerator

No SequencedSet Functionality C# doesn't provide an interface akin to the Java 21 SequencedSet interface

**Alternatives**

If you frequently need direct 'first' access and set-like uniqueness, you might consider a custom data structure that combines aspects of a dictionary (for quick lookups) and a linked list (for ordering)
