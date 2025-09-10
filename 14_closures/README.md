# Closures - Functions That Remember Their Environment

This folder teaches you about closures - one of Go's most powerful features. A closure is a function that "captures" and remembers variables from its surrounding environment, even after that environment has finished executing.

## What This Teaches You

**Closure Concepts:**
- Functions that capture variables from their surrounding scope
- How closures maintain access to outer variables even after the outer function returns
- Creating factory functions that generate customized functions
- Common pitfalls with loop variables and closures
- Real-world applications like callbacks, event handlers, and stateful functions

## The Code Explained Simply

```go
package main
import "fmt"

// This function returns another function (a closure)
func counter() func() int {
    var count int = 0        // This variable belongs to the counter function
    
    // Return an anonymous function that "captures" the count variable
    return func() int {
        count += 1           // The inner function can access and modify count
        return count         // Even after counter() finishes, count persists!
    }
}

func main() {
    increment := counter()   // Get a closure that remembers its own count
    
    fmt.Println(increment()) // Prints: 1 (count was 0, now 1)
    fmt.Println(increment()) // Prints: 2 (count was 1, now 2)
    
    // Each closure has its own copy of the variables
    anotherIncrement := counter()  // Get a separate counter
    fmt.Println(anotherIncrement()) // Prints: 1 (its own count starts at 0)
    fmt.Println(increment())        // Prints: 3 (original counter continues)
}
```

## Understanding How Closures Work

### Think of Closures Like This:
Imagine you have a **private workspace** (the outer function) with some **tools and materials** (variables). You create a **worker** (the inner function) who works in that space. Even after you leave the workspace, the worker **remembers where everything is** and can continue using those tools.

### What Makes a Closure:
1. **An inner function** defined inside another function
2. **The inner function uses variables** from the outer function  
3. **The outer function returns the inner function**
4. **The returned function "remembers" the outer variables**

### Simple Example Breakdown:
```go
func makeGreeter(greeting string) func(string) string {
    // 'greeting' is captured by the closure
    return func(name string) string {
        return greeting + " " + name + "!"  // Uses captured 'greeting'
    }
}

// Usage:
hello := makeGreeter("Hello")        // Creates closure that remembers "Hello"
goodbye := makeGreeter("Goodbye")    // Creates closure that remembers "Goodbye"

fmt.Println(hello("Alice"))     // "Hello Alice!"
fmt.Println(goodbye("Bob"))     // "Goodbye Bob!"
fmt.Println(hello("Charlie"))   // "Hello Charlie!" - still remembers "Hello"
```

## Different Types of Closures

### 1. **State-Keeping Closures (Counters, Accumulators)**
```go
func makeAdder(initialValue int) func(int) int {
    sum := initialValue  // Captured variable that persists
    
    return func(value int) int {
        sum += value     // Modify the captured variable
        return sum       // Return updated sum
    }
}

// Usage:
adder := makeAdder(10)       // Start with 10
fmt.Println(adder(5))        // 15 (10 + 5)
fmt.Println(adder(3))        // 18 (15 + 3)
fmt.Println(adder(2))        // 20 (18 + 2)

// Each closure maintains its own state
anotherAdder := makeAdder(100)
fmt.Println(anotherAdder(1))  // 101 (separate from first adder)
```

### 2. **Configuration Closures**
```go
func makeValidator(minLength int, maxLength int) func(string) bool {
    // Capture validation rules
    return func(input string) bool {
        length := len(input)
        return length >= minLength && length <= maxLength
    }
}

// Create different validators
usernameValidator := makeValidator(3, 20)    // Username: 3-20 chars
passwordValidator := makeValidator(8, 50)    // Password: 8-50 chars

// Use them
fmt.Println(usernameValidator("john"))       // true (4 chars, valid)
fmt.Println(passwordValidator("123"))        // false (3 chars, too short)
fmt.Println(passwordValidator("mypassword")) // true (10 chars, valid)
```

### 3. **Event Handler Closures**
```go
func makeButtonHandler(buttonName string, clickCount *int) func() {
    return func() {
        *clickCount++
        fmt.Printf("%s button clicked! Total clicks: %d\n", buttonName, *clickCount)
    }
}

// Simulate button clicks
var totalClicks int
saveHandler := makeButtonHandler("Save", &totalClicks)
cancelHandler := makeButtonHandler("Cancel", &totalClicks)

saveHandler()    // "Save button clicked! Total clicks: 1"
saveHandler()    // "Save button clicked! Total clicks: 2"  
cancelHandler()  // "Cancel button clicked! Total clicks: 3"
```

### 4. **Factory Function Closures**
```go
func makeMathOperation(operation string) func(float64, float64) float64 {
    switch operation {
    case "add":
        return func(a, b float64) float64 { return a + b }
    case "subtract":
        return func(a, b float64) float64 { return a - b }
    case "multiply":
        return func(a, b float64) float64 { return a * b }
    case "divide":
        return func(a, b float64) float64 { 
            if b != 0 {
                return a / b 
            }
            return 0
        }
    default:
        return func(a, b float64) float64 { return 0 }
    }
}

// Create different calculators
add := makeMathOperation("add")
multiply := makeMathOperation("multiply")

fmt.Println(add(5, 3))        // 8
fmt.Println(multiply(4, 7))   // 28
```

## Real-World Examples

### 1. **HTTP Request Builder**
```go
func makeAPIClient(baseURL string, apiKey string) func(string) string {
    return func(endpoint string) string {
        fullURL := baseURL + endpoint + "?api_key=" + apiKey
        // In real code, you'd make HTTP request here
        return fmt.Sprintf("Making request to: %s", fullURL)
    }
}

// Create API clients for different services
githubAPI := makeAPIClient("https://api.github.com", "github_key_123")
weatherAPI := makeAPIClient("https://api.weather.com", "weather_key_456")

// Use them
fmt.Println(githubAPI("/users"))     // "Making request to: https://api.github.com/users?api_key=github_key_123"
fmt.Println(weatherAPI("/forecast")) // "Making request to: https://api.weather.com/forecast?api_key=weather_key_456"
```

### 2. **Database Connection Pool**
```go
func makeDBConnection(connectionString string, maxConnections int) func() string {
    currentConnections := 0
    
    return func() string {
        if currentConnections < maxConnections {
            currentConnections++
            return fmt.Sprintf("Connected to %s (connection %d/%d)", 
                connectionString, currentConnections, maxConnections)
        }
        return "Connection pool full"
    }
}

// Create connection pool
dbConnect := makeDBConnection("postgres://localhost:5432/mydb", 3)

fmt.Println(dbConnect()) // "Connected to postgres://localhost:5432/mydb (connection 1/3)"
fmt.Println(dbConnect()) // "Connected to postgres://localhost:5432/mydb (connection 2/3)"
fmt.Println(dbConnect()) // "Connected to postgres://localhost:5432/mydb (connection 3/3)"
fmt.Println(dbConnect()) // "Connection pool full"
```

### 3. **Rate Limiter**
```go
func makeRateLimiter(maxRequests int, windowSeconds int) func() bool {
    requests := 0
    windowStart := time.Now()
    
    return func() bool {
        now := time.Now()
        
        // Reset window if time has passed
        if now.Sub(windowStart).Seconds() >= float64(windowSeconds) {
            requests = 0
            windowStart = now
        }
        
        if requests < maxRequests {
            requests++
            return true  // Request allowed
        }
        return false  // Rate limit exceeded
    }
}

// Allow 5 requests per 10 seconds
rateLimiter := makeRateLimiter(5, 10)

for i := 0; i < 7; i++ {
    if rateLimiter() {
        fmt.Printf("Request %d: Allowed\n", i+1)
    } else {
        fmt.Printf("Request %d: Rate limited\n", i+1)
    }
}
```

### 4. **Middleware Creator**
```go
func makeLogger(prefix string) func(string) {
    requestCount := 0
    
    return func(message string) {
        requestCount++
        timestamp := time.Now().Format("15:04:05")
        fmt.Printf("[%s] %s #%d: %s\n", timestamp, prefix, requestCount, message)
    }
}

// Create different loggers
authLogger := makeLogger("AUTH")
dbLogger := makeLogger("DATABASE")

authLogger("User login attempt")     // "[15:04:05] AUTH #1: User login attempt"
dbLogger("Query executed")           // "[15:04:05] DATABASE #1: Query executed"
authLogger("Login successful")       // "[15:04:05] AUTH #2: Login successful"
```

## The Loop Variable Capture Pitfall

### Common Mistake (The Loop Variable Problem):
```go
// ❌ This doesn't work as expected!
func makeFunctions() []func() int {
    functions := make([]func() int, 0)
    
    for i := 0; i < 5; i++ {
        // All closures capture the SAME variable 'i'
        fn := func() int {
            return i  // By the time this runs, i = 5 (end of loop)
        }
        functions = append(functions, fn)
    }
    return functions
}

// All functions return 5!
funcs := makeFunctions()
for _, fn := range funcs {
    fmt.Println(fn())  // Prints: 5, 5, 5, 5, 5
}
```

### Solutions:

#### Solution 1: Create New Variable Each Iteration
```go
// ✅ Create a new variable each iteration
func makeFunctions() []func() int {
    functions := make([]func() int, 0)
    
    for i := 0; i < 5; i++ {
        i := i  // Create new 'i' variable for this iteration
        fn := func() int {
            return i  // Now captures the iteration-specific 'i'
        }
        functions = append(functions, fn)
    }
    return functions
}
```

#### Solution 2: Pass Value to Closure
```go
// ✅ Pass the value directly to the closure
func makeFunctions() []func() int {
    functions := make([]func() int, 0)
    
    for i := 0; i < 5; i++ {
        fn := func(value int) func() int {
            return func() int {
                return value  // Captures 'value', not loop variable
            }
        }(i)  // Immediately call with current 'i'
        functions = append(functions, fn)
    }
    return functions
}

// Now prints: 0, 1, 2, 3, 4
funcs := makeFunctions()
for _, fn := range funcs {
    fmt.Println(fn())
}
```

## Key Concepts to Remember

### 1. **Closures Capture Variables, Not Values**
```go
x := 10
closure := func() int { return x }

x = 20  // Change x after creating closure
fmt.Println(closure())  // Prints: 20 (not 10!)
```

### 2. **Each Closure Call to Same Function Creates New Environment**
```go
makeCounter := func() func() int {
    count := 0
    return func() int {
        count++
        return count
    }
}

counter1 := makeCounter()  // Independent counter
counter2 := makeCounter()  // Another independent counter

fmt.Println(counter1())  // 1
fmt.Println(counter2())  // 1 (separate from counter1)
fmt.Println(counter1())  // 2
```

### 3. **Closures Can Modify Captured Variables**
```go
balance := 100.0

deposit := func(amount float64) {
    balance += amount  // Modifies the captured variable
}

withdraw := func(amount float64) bool {
    if balance >= amount {
        balance -= amount
        return true
    }
    return false
}

deposit(50)           // balance = 150
fmt.Println(withdraw(30))  // true, balance = 120
fmt.Println(balance)       // 120
```

## When to Use Closures

### ✅ Good Use Cases:
- **Callbacks and event handlers**
- **Configuration functions** (validators, formatters)
- **Stateful functions** (counters, accumulators)
- **Factory functions** that create specialized functions
- **Middleware and decorators**
- **Private state** without using structs

### ❌ Avoid When:
- Simple functions without state
- Functions that don't need to capture variables
- When regular structs with methods are clearer
- Performance-critical code (closures have slight overhead)

## Memory Considerations

### Closures Keep Variables Alive:
```go
func createLargeClosure() func() {
    largeData := make([]int, 1000000)  // Large slice
    
    return func() {
        // Even if we only use a small part...
        fmt.Println("First element:", largeData[0])
        // The entire largeData slice stays in memory!
    }
}
```

### Be Mindful of What You Capture:
```go
// ❌ Captures entire large object
func badClosure(user LargeUserObject) func() string {
    return func() string {
        return user.Name  // Keeps entire user object in memory
    }
}

// ✅ Only capture what you need
func goodClosure(user LargeUserObject) func() string {
    name := user.Name  // Extract just the name
    return func() string {
        return name  // Only keeps the string in memory
    }
}
```

## How to Run This

```powershell
cd 14_closures
go run .
```

## What You Should See

```
1
2
```

## Best Practices

1. **Be explicit about what you capture** - only capture variables you actually use
2. **Watch out for loop variables** - create new variables when needed  
3. **Use closures for stateful operations** where structs might be overkill
4. **Consider memory usage** - closures keep captured variables alive
5. **Name your closures** when storing them in variables for clarity
6. **Test closure behavior** - make sure they capture what you expect

## Common Patterns

### Decorator Pattern:
```go
func withLogging(fn func(string) string) func(string) string {
    return func(input string) string {
        fmt.Printf("Calling function with input: %s\n", input)
        result := fn(input)
        fmt.Printf("Function returned: %s\n", result)
        return result
    }
}
```

### Memoization (Caching):
```go
func memoize(fn func(int) int) func(int) int {
    cache := make(map[int]int)
    
    return func(input int) int {
        if result, exists := cache[input]; exists {
            return result
        }
        result := fn(input)
        cache[input] = result
        return result
    }
}
```

Closures are like giving functions a **photographic memory** - they remember the environment where they were created and can use that memory throughout their entire lifetime, even long after their birthplace has disappeared!
