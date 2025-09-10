# Simple Values - Go's Basic Data Types

This folder teaches you about the fundamental building blocks of data in Go - the basic types you'll use everywhere.

## What This Teaches You

**The Basic Data Types:**
- **Numbers**: Integers (whole numbers) and floats (decimal numbers)
- **Strings**: Text wrapped in double quotes
- **Booleans**: True or false values
- **Basic math operations**: Addition, division, etc.

## The Code Explained Simply

```go
package main
import "fmt"

func main() {
    // Numbers - integers (whole numbers)
    fmt.Println(1 + 1)           // Prints: 2
    
    // Strings - text in double quotes
    fmt.Println("hello golang")   // Prints: hello golang
    
    // Booleans - true or false
    fmt.Println(true)            // Prints: true
    fmt.Println(false)           // Prints: false
    
    // Floats - numbers with decimal points
    fmt.Println(10.5)            // Prints: 10.5
    fmt.Println(7.0 / 3.0)       // Prints: 2.3333333333333335
}
```

## Understanding Each Type

### Integers (Whole Numbers)
- Examples: `1`, `42`, `-17`, `0`
- Used for counting, indexing, IDs
- Operations: `+`, `-`, `*`, `/`, `%`

### Strings (Text)
- Always use **double quotes**: `"hello"`
- Can contain letters, numbers, spaces, symbols
- Examples: `"Go is awesome"`, `"user@email.com"`, `"123"`

### Booleans (True/False)
- Only two values: `true` or `false`
- Used for conditions, flags, yes/no questions
- Result of comparisons like `5 > 3` (which is `true`)

### Floats (Decimal Numbers)
- Examples: `3.14`, `10.5`, `-2.7`
- Used for measurements, percentages, precise calculations
- Division of integers gives integers, but floats give precise results

## Key Concepts to Remember

1. **Zero Values**: Every type has a default "empty" value:
   - Numbers: `0`
   - Strings: `""` (empty string)
   - Booleans: `false`

2. **Type Matters**: Go cares about types - you can't mix them without converting
   - You can't add a number to a string directly
   - You can't assign a string to a number variable

3. **String Quotes**: Always use double quotes `"text"`, not single quotes `'text'`

## Common Operations You'll Use

```go
// Math with numbers
fmt.Println(10 + 5)    // Addition: 15
fmt.Println(10 - 5)    // Subtraction: 5  
fmt.Println(10 * 5)    // Multiplication: 50
fmt.Println(10 / 5)    // Division: 2
fmt.Println(10 % 3)    // Remainder: 1

// Combining strings
fmt.Println("Hello " + "World")  // Prints: Hello World

// Comparing values (returns true/false)
fmt.Println(5 > 3)     // true
fmt.Println(5 == 3)    // false
fmt.Println("a" == "a") // true
```

## How to Run This

```powershell
cd 2_simple_values
go run .
```

## What You Should See

```
2
hello golang
true
false
10.5
2.3333333333333335
```

## Real-World Connection

These basic types are like the atoms of programming:
- **Integers**: User IDs, quantities, scores
- **Strings**: Names, emails, messages
- **Booleans**: Is logged in? Is valid? Is complete?
- **Floats**: Prices, measurements, percentages

Every complex program is built using combinations of these simple values!
