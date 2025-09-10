# If/Else - Making Decisions in Your Code

This folder teaches you how to make your programs smart by adding decision-making logic. If/else statements let your code choose different paths based on conditions.

## What This Teaches You

**Conditional Logic Fundamentals:**
- **Simple if**: Do something only when a condition is true
- **If-else**: Choose between two different actions
- **If-else if**: Handle multiple different conditions  
- **Logical operators**: Combine multiple conditions with AND (`&&`) and OR (`||`)
- **Short statement**: Declare a variable inside the if statement

## The Code Explained Simply

```go
package main
import "fmt"

func main() {
    // Simple if statement
    age := 16
    if age >= 18 {
        fmt.Println("person is an adult")
    } else {
        fmt.Println("person is not an adult")
    }
    // Prints: person is not an adult
    
    // Multiple conditions with else if
    age = 10
    if age >= 18 {
        fmt.Println("person is an adult")
    } else if age >= 12 {
        fmt.Println("person is teenager") 
    } else {
        fmt.Println("person is a kid")
    }
    // Prints: person is a kid
    
    // Combining conditions with AND (&&)
    role := "admin"
    hasPermissions := false
    if role == "admin" && hasPermissions {  // Both must be true
        fmt.Println("Access granted")
    } else {
        fmt.Println("Access denied")
    }
    // Prints: Access denied (because hasPermissions is false)
    
    // Short statement: declare variable inside if
    if age := 20; age >= 18 {  // Declare age and use it immediately
        fmt.Println("person is an adult", age)
    } else if age >= 12 {
        fmt.Println("person is teenager", age)
    }
    // age variable only exists inside these if/else blocks
}
```

## Understanding Conditions

### Basic Comparisons
```go
age := 25
score := 85
name := "Alice"

// Equality
if age == 25 { }          // Is age exactly 25?
if name == "Alice" { }    // Is name exactly "Alice"?

// Inequality  
if age != 30 { }          // Is age NOT 30?
if name != "Bob" { }      // Is name NOT "Bob"?

// Numerical comparisons
if age > 18 { }           // Is age greater than 18?
if age < 65 { }           // Is age less than 65?
if score >= 90 { }        // Is score 90 or higher?
if score <= 100 { }       // Is score 100 or lower?
```

### Logical Operators

#### AND operator (`&&`) - Both conditions must be true
```go
age := 25
hasLicense := true

if age >= 18 && hasLicense {
    fmt.Println("Can drive a car")  // Only prints if BOTH are true
}

// More examples
if score >= 60 && attendance >= 80 {
    fmt.Println("Student passes")   // Need good score AND good attendance
}

if isWeekend && hasFreetime {
    fmt.Println("Let's go to the movies")  // Only if it's weekend AND free
}
```

#### OR operator (`||`) - At least one condition must be true
```go
weather := "sunny"
temperature := 75

if weather == "sunny" || temperature > 70 {
    fmt.Println("Great day for outdoor activities")  // Prints if EITHER is true
}

// More examples  
if isManager || isOwner {
    fmt.Println("Has admin access")  // Either role gives access
}

if emergency || vipCustomer {
    fmt.Println("Handle immediately")  // Urgent if EITHER condition
}
```

#### NOT operator (`!`) - Flip true/false
```go
isLoggedIn := false

if !isLoggedIn {  // If NOT logged in
    fmt.Println("Please log in first")
}

// More examples
if !hasPermission {
    fmt.Println("Access denied")
}

if !(age >= 18) {  // Same as: if age < 18
    fmt.Println("Too young")
}
```

## Different If Statement Patterns

### 1. Simple If (No Else)
```go
temperature := 32
if temperature <= 32 {
    fmt.Println("Water freezes!")  // Only runs when condition is true
}
// Program continues regardless
```

### 2. If-Else (Two Choices)
```go
balance := 50.00
purchaseAmount := 75.00

if balance >= purchaseAmount {
    fmt.Println("Purchase approved")
    balance -= purchaseAmount
} else {
    fmt.Println("Insufficient funds")
}
```

### 3. If-Else If Chain (Multiple Choices)
```go
grade := 85

if grade >= 90 {
    fmt.Println("Grade: A")
} else if grade >= 80 {
    fmt.Println("Grade: B")      // This runs (85 >= 80)
} else if grade >= 70 {
    fmt.Println("Grade: C")
} else if grade >= 60 {
    fmt.Println("Grade: D") 
} else {
    fmt.Println("Grade: F")
}
```

### 4. Short Statement If
**Declare and use a variable in the same line:**
```go
// Instead of this:
user := getUser()
if user.IsActive {
    // do something
}

// You can write this:
if user := getUser(); user.IsActive {  // Declare user and check it immediately
    // do something
    // user is available here
} else {
    // user is also available here
}
// user is NOT available here (scope ends)
```

## Real-World Examples

### User Authentication
```go
username := "john"
password := "secret123"
isActive := true

if username != "" && password != "" && isActive {
    fmt.Println("Login successful")
    // Redirect to dashboard
} else if !isActive {
    fmt.Println("Account is disabled")
} else {
    fmt.Println("Invalid credentials")
}
```

### E-commerce Pricing
```go
itemPrice := 99.99
customerType := "premium"
orderAmount := 150.00

if customerType == "premium" {
    itemPrice *= 0.9  // 10% discount for premium customers
    fmt.Println("Premium discount applied")
}

if orderAmount >= 100 {
    fmt.Println("Free shipping!")
} else {
    shippingCost := 5.99
    fmt.Printf("Shipping cost: $%.2f\n", shippingCost)
}
```

### Game Logic
```go
playerHealth := 75
playerLevel := 12
hasSpecialItem := true

if playerHealth <= 0 {
    fmt.Println("Game Over!")
} else if playerHealth <= 20 {
    fmt.Println("Warning: Low health!")
} 

if playerLevel >= 10 && hasSpecialItem {
    fmt.Println("You can enter the secret area!")
} else if playerLevel >= 10 {
    fmt.Println("You need a special item to proceed")
} else {
    fmt.Println("Reach level 10 first")
}
```

### Input Validation
```go
age := -5

if age < 0 {
    fmt.Println("Error: Age cannot be negative")
} else if age > 150 {
    fmt.Println("Error: Please enter a realistic age")
} else if age < 13 {
    fmt.Println("Child account created")
} else if age < 18 {
    fmt.Println("Teen account created - parental consent required")
} else {
    fmt.Println("Adult account created")
}
```

## Key Concepts to Remember

1. **Go doesn't have ternary operator** - Use regular if/else instead
2. **Conditions must evaluate to true or false** (boolean values)
3. **Use parentheses** to group complex conditions: `if (a && b) || (c && d) {}`
4. **Variables in short statement if** only exist inside the if/else blocks
5. **Else if conditions are checked in order** - first match wins

## Common Beginner Mistakes

### 1. Assignment vs Comparison
```go
age := 25

// ❌ Wrong - this assigns 18 to age!
if age = 18 {  // Single = is assignment
    fmt.Println("Adult")
}

// ✅ Correct - this compares age to 18
if age == 18 {  // Double == is comparison
    fmt.Println("Just turned adult")
}
```

### 2. String Comparison Case Sensitivity
```go
userInput := "YES"

// ❌ This won't match
if userInput == "yes" {  // Case sensitive!
    fmt.Println("Confirmed")
}

// ✅ Convert to lowercase first
if strings.ToLower(userInput) == "yes" {
    fmt.Println("Confirmed")
}
```

### 3. Logical Operator Confusion
```go
age := 25
hasLicense := false

// ❌ This is wrong logic  
if age >= 18 || hasLicense {  // OR means EITHER condition
    fmt.Println("Can drive")  // This prints even without license!
}

// ✅ Correct - need BOTH conditions
if age >= 18 && hasLicense {  // AND means BOTH conditions
    fmt.Println("Can drive")
}
```

## How to Run This

```powershell
cd 6_if_else
go run .
```

## Best Practices

1. **Keep conditions simple and readable**
2. **Use parentheses for complex logic**: `if (a && b) || (c && d) {}`
3. **Handle error cases first** (early returns)
4. **Use else if for mutually exclusive conditions**
5. **Consider switch statements** for many conditions on the same variable

If/else statements are like the traffic lights of your code - they direct the flow of your program down different paths based on the current situation!
