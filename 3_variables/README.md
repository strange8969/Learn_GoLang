# Variables - Storing and Managing Data

This folder teaches you how to create containers for your data - think of variables as labeled boxes where you can store values and change them later.

## What This Teaches You

**Different Ways to Create Variables:**
- `var` keyword for explicit declaration
- Type inference when Go can guess the type
- Short declaration `:=` for quick variable creation inside functions
- How to declare variables without knowing their values at first and assigning them later?

## The Code Explained Simply

The code shows different ways to create the same variable:

```go
// Method 1: Full explicit declaration
var name string = "golang"    // Create a string variable called "name"

// Method 2: Let Go guess the type (inference)
var name = "golang"          // Go sees "golang" and knows it's a string

// Method 3: Short declaration (only inside functions)
name := "golang"             // Shortest way - Go creates and assigns

// Method 4: Declare first, assign later
var name string              // Create empty string variable
name = "golang"              // Put value in it later
```

## Understanding Each Method

### Method 1: Full Declaration `var name type = value`
- **When to use**: When you want to be very clear about the type
- **Example**: `var age int = 25`
- Most verbose but clearest for beginners

### Method 2: Type Inference `var name = value`
- **When to use**: When the type is obvious from the value
- **Example**: `var price = 19.99` (Go knows it's a float)
- Shorter but still explicit

### Method 3: Short Declaration `name := value`
- **When to use**: Inside functions when you want to be quick
- **Example**: `username := "john_doe"`
- Shortest and most common in real Go code
- **Important**: Only works inside functions, not at package level

### Method 4: Declare Then Assign
- **When to use**: When you don't know the value yet
- **Example**: 
```go
var result string        // Create empty variable
if condition {
    result = "success"   // Assign value later
} else {
    result = "failed"
}
```

## Key Concepts to Remember

### 1. **Zero Values** - Empty Variables Have Default Values
```go
var age int        // age = 0 (integers default to 0)
var name string    // name = "" (strings default to empty)
var isReady bool   // isReady = false (booleans default to false)
var price float64  // price = 0.0 (floats default to 0.0)
```

### 2. **Variable Naming Rules**
- Must start with a letter or underscore: `name`, `_temp`, `userName` ✓
- Can contain letters, numbers, underscores: `user1`, `max_age` ✓
- Cannot start with numbers: `1user` ✗
- Cannot use Go keywords: `var`, `func`, `if` ✗

### 3. **Variables Can Change**
```go
name := "John"       // name is "John"
name = "Jane"        // Now name is "Jane"
age := 25           // age is 25
age = age + 1       // Now age is 26
```

## Common Patterns You'll Use

### Multiple Variables at Once
```go
// Multiple declarations
var name, email string
var age, score int

// Multiple assignments
name, age := "Alice", 30

// Swapping values
a, b := 1, 2
a, b = b, a    // Now a=2, b=1
```

### Different Types Together
```go
var (
    name     string = "Go Programming"
    version  int    = 2
    isActive bool   = true
    price    float64 = 99.99
)
```

## Real-World Examples

```go
// User information
username := "john_doe"
email := "john@example.com"
age := 28
isVerified := false

// Shopping cart
itemName := "Laptop"
quantity := 1
price := 999.99
totalCost := price * float64(quantity)

// Game score
playerName := "Alice"
currentLevel := 5
highScore := 1500
hasWon := currentLevel > 10
```

## Common Beginner Mistakes

1. **Using `:=` at package level** (outside functions)
   ```go
   package main
   name := "test"  // ❌ Error! Use var instead
   var name = "test" // ✅ Correct
   ```

2. **Declaring variable twice**
   ```go
   name := "John"
   name := "Jane"  // ❌ Error! name already declared
   name = "Jane"   // ✅ Correct - just assign new value
   ```

3. **Wrong type assignment**
   ```go
   var age int = "25"    // ❌ Error! Can't put string in int variable
   var age int = 25      // ✅ Correct
   ```

## How to Run This

```powershell
cd 3_variables
go run .
```

## Practical Tips

- **Inside functions**: Use `:=` for quick variables
- **At package level**: Use `var` declarations  
- **When type isn't obvious**: Be explicit with `var name type = value`
- **For groups of related variables**: Use `var ()` block syntax

Variables are like labeled storage containers - you create them once and can put different values in them throughout your program's life!
