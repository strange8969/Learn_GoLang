# Variadic Functions - Functions That Accept Any Number of Arguments

This folder teaches you how to create functions that can accept a flexible number of arguments of the same type. Think of it like a function that can handle 1 argument, 5 arguments, or even 100 arguments!

## What This Teaches You

**Variadic Function Concepts:**
- Functions that accept zero or more arguments of the same type
- How to use the `...` syntax (called "ellipsis" or "three dots")
- How to pass slices to variadic functions with `...` operator
- How to iterate through variadic parameters inside the function

## The Code Explained Simply

```go
package main
import "fmt"

// Variadic function - accepts any number of integers
func sum(nums ...int) int {    // ...int means "zero or more integers"
    total := 0
    
    // Inside the function, nums is just a slice []int
    for _, num := range nums {  // Loop through each number
        total = total + num     // Add each number to total
    }
    
    return total
}

func main() {
    // You can call it with different numbers of arguments:
    
    // With individual arguments
    result1 := sum(1, 2, 3)           // Pass 3 numbers: result = 6
    result2 := sum(5, 10, 15, 20)     // Pass 4 numbers: result = 50
    result3 := sum(100)               // Pass 1 number: result = 100
    result4 := sum()                  // Pass 0 numbers: result = 0
    
    // With a slice using ... operator
    nums := []int{3, 4, 5, 6}         // Create a slice
    result5 := sum(nums...)           // Expand slice into arguments: result = 18
    
    fmt.Println(result5)              // Prints: 18
}
```

## Understanding the Syntax

### Function Definition: `func name(params ...type)`
```go
func functionName(regularParam int, variadicParam ...string) {
    // variadicParam is a slice: []string
    // It contains all the string arguments passed to the function
}
```

### Key Rules:
1. **Only the last parameter** can be variadic
2. **There can be only one** variadic parameter per function
3. **Inside the function**, the variadic parameter is a slice

### Examples:
```go
// ✅ Correct - variadic parameter last
func process(name string, numbers ...int) { }

// ✅ Correct - only variadic parameter  
func sum(numbers ...int) { }

// ❌ Wrong - variadic not last
func process(numbers ...int, name string) { }

// ❌ Wrong - multiple variadic parameters
func process(nums1 ...int, nums2 ...int) { }
```

## Different Ways to Call Variadic Functions

### 1. Individual Arguments
```go
func greet(greeting string, names ...string) {
    for _, name := range names {
        fmt.Printf("%s %s!\n", greeting, name)
    }
}

// Call with different numbers of names
greet("Hello", "Alice")                    // 1 name
greet("Hi", "Bob", "Charlie")              // 2 names  
greet("Welcome", "Alice", "Bob", "Charlie", "David")  // 4 names
greet("Hey")                               // 0 names (just greeting)
```

### 2. Slice with Spread Operator (`...`)
```go
friends := []string{"Alice", "Bob", "Charlie"}
greet("Hello", friends...)  // Equivalent to: greet("Hello", "Alice", "Bob", "Charlie")

numbers := []int{10, 20, 30, 40, 50}
total := sum(numbers...)    // Equivalent to: sum(10, 20, 30, 40, 50)
```

### 3. Mixed Individual and Slice (with some creativity)
```go
// You can't directly mix, but you can build a slice first
baseNumbers := []int{1, 2, 3}
moreNumbers := []int{4, 5, 6}
allNumbers := append(baseNumbers, moreNumbers...)  // Combine slices
result := sum(allNumbers...)                       // Pass combined slice
```

## Real-World Examples

### 1. Logging Function
```go
func log(level string, messages ...string) {
    timestamp := time.Now().Format("2006-01-02 15:04:05")
    fmt.Printf("[%s] %s: ", timestamp, level)
    
    for i, message := range messages {
        fmt.Print(message)
        if i < len(messages)-1 {
            fmt.Print(" | ")  // Separate multiple messages
        }
    }
    fmt.Println()
}

// Usage:
log("INFO", "Server started")
log("ERROR", "Database connection failed", "Retrying in 5 seconds")
log("DEBUG", "User login attempt", "Username: john", "IP: 192.168.1.1")
```

### 2. Mathematical Operations
```go
func average(numbers ...float64) float64 {
    if len(numbers) == 0 {
        return 0  // Handle empty case
    }
    
    total := 0.0
    for _, num := range numbers {
        total += num
    }
    return total / float64(len(numbers))
}

func max(numbers ...int) int {
    if len(numbers) == 0 {
        return 0  // or panic, or return error
    }
    
    maximum := numbers[0]  // Start with first number
    for _, num := range numbers[1:] {  // Check rest of numbers
        if num > maximum {
            maximum = num
        }
    }
    return maximum
}

// Usage:
avg1 := average(85.5, 92.0, 78.5)              // 85.33
avg2 := average(100, 95, 90, 85, 80)           // 90.0
maxNum := max(45, 23, 89, 12, 67)              // 89
```

### 3. String Formatting and Joining
```go
func createURL(baseURL string, pathSegments ...string) string {
    url := baseURL
    for _, segment := range pathSegments {
        if !strings.HasSuffix(url, "/") {
            url += "/"
        }
        url += segment
    }
    return url
}

func formatMessage(template string, values ...interface{}) string {
    // Use fmt.Sprintf with variadic arguments
    return fmt.Sprintf(template, values...)
}

// Usage:
apiURL := createURL("https://api.example.com", "users", "123", "posts")
// Result: "https://api.example.com/users/123/posts"

message := formatMessage("User %s has %d messages", "Alice", 5)
// Result: "User Alice has 5 messages"
```

### 4. Validation Functions
```go
func validateRequired(fieldName string, values ...string) error {
    for _, value := range values {
        if strings.TrimSpace(value) == "" {
            return fmt.Errorf("%s cannot be empty", fieldName)
        }
    }
    return nil  // All values are valid
}

// Usage:
err1 := validateRequired("Name", "John Doe")           // OK
err2 := validateRequired("Address", "", "123 Main St") // Error: Address cannot be empty
```

## Key Concepts to Remember

### 1. Inside Function, Variadic Parameter is a Slice
```go
func process(items ...string) {
    fmt.Printf("Type: %T\n", items)      // Type: []string
    fmt.Printf("Length: %d\n", len(items))
    fmt.Printf("Capacity: %d\n", cap(items))
    
    // You can use all slice operations:
    if len(items) > 0 {
        fmt.Println("First item:", items[0])
    }
    
    // Slice operations work
    if len(items) > 2 {
        middle := items[1:len(items)-1]
        fmt.Println("Middle items:", middle)
    }
}
```

### 2. Empty Variadic Parameters
```go
func announceWinners(event string, winners ...string) {
    fmt.Printf("Event: %s\n", event)
    
    if len(winners) == 0 {
        fmt.Println("No winners yet!")
        return
    }
    
    fmt.Printf("Winners (%d): ", len(winners))
    for i, winner := range winners {
        fmt.Print(winner)
        if i < len(winners)-1 {
            fmt.Print(", ")
        }
    }
    fmt.Println()
}

// Works with no variadic arguments
announceWinners("Lottery")  // Event: Lottery\nNo winners yet!
```

### 3. Combining with Regular Parameters
```go
func sendEmail(subject string, body string, recipients ...string) {
    fmt.Printf("Subject: %s\n", subject)
    fmt.Printf("Body: %s\n", body)
    fmt.Printf("Sending to %d recipients:\n", len(recipients))
    
    for _, email := range recipients {
        fmt.Printf("  - %s\n", email)
    }
}

// Usage:
sendEmail(
    "Meeting Reminder", 
    "Don't forget about tomorrow's meeting",
    "alice@company.com", 
    "bob@company.com", 
    "charlie@company.com",
)
```

## Common Use Cases for Variadic Functions

1. **Logging and Debugging**: Accept multiple messages
2. **Mathematical Functions**: Sum, average, min, max of multiple numbers
3. **String Processing**: Join, format, concatenate multiple strings
4. **Validation**: Check multiple values at once
5. **Collection Operations**: Process multiple items of the same type
6. **Configuration**: Accept multiple options or settings

## Common Beginner Mistakes

### 1. Forgetting `...` When Passing Slice
```go
numbers := []int{1, 2, 3, 4, 5}

// ❌ Wrong - passes slice as single argument
result := sum(numbers)  // Compile error!

// ✅ Correct - expands slice into multiple arguments
result := sum(numbers...)
```

### 2. Trying Multiple Variadic Parameters
```go
// ❌ This doesn't work
func process(nums ...int, names ...string) {  // Compile error!
}

// ✅ Use a struct or separate functions instead
type Data struct {
    Numbers []int
    Names   []string
}

func process(data Data) {
    // Process data.Numbers and data.Names
}
```

### 3. Not Handling Empty Case
```go
func average(numbers ...float64) float64 {
    total := 0.0
    for _, num := range numbers {
        total += num
    }
    // ❌ Division by zero if no numbers passed!
    return total / float64(len(numbers))
}

// ✅ Better version
func average(numbers ...float64) float64 {
    if len(numbers) == 0 {
        return 0  // or return an error
    }
    
    total := 0.0
    for _, num := range numbers {
        total += num
    }
    return total / float64(len(numbers))
}
```

## How to Run This

```powershell
cd 13_variadic_functions
go run .
```

## What You Should See

```
18
```

## Best Practices

1. **Document expected behavior** when no arguments are passed
2. **Validate input** - check for empty cases
3. **Use meaningful parameter names** that indicate what the function expects
4. **Consider using slices directly** if you always pass collections
5. **Combine with interfaces** for maximum flexibility (like `fmt.Printf`)

Variadic functions are like expandable containers - they can hold just what you need, whether that's one item or a hundred items, making your functions more flexible and user-friendly!
