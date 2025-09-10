# Switch - Clean Multi-Way Decision Making

This folder teaches you about switch statements - a cleaner way to handle multiple conditions on the same value. Instead of long chains of if-else statements, switch makes your code more readable and organized.

## What This Teaches You

**Switch Statement Types:**
- **Simple switch**: Compare one variable against multiple values
- **Multiple condition switch**: Check several values in one case  
- **Type switch**: Handle different data types (interfaces)
- **Conditionless switch**: Use switch like if-else chains but cleaner

## The Code Explained Simply

```go
package main
import ("fmt", "time")

func main() {
    // Simple switch - compare one value
    i := 3
    switch i {
    case 1:
        fmt.Println("one")
    case 2:
        fmt.Println("two") 
    case 3:
        fmt.Println("three")     // This runs because i = 3
    default:
        fmt.Println("other")
    }
    // Prints: three
    
    // Multiple conditions in one case
    switch time.Now().Weekday() {
    case time.Saturday, time.Sunday:     // Either Saturday OR Sunday
        fmt.Println("it's weekend")      // Runs for both days
    default:
        fmt.Println("it's workday")
    }
    
    // Type switch - handle different types
    whoAmI := func(i interface{}) {      // interface{} accepts any type
        switch i.(type) {                // Check the actual type
        case int:
            fmt.Println("its an integer")
        case string:
            fmt.Println("its a string") 
        case bool:
            fmt.Println("its a boolean")
        default:
            fmt.Println("other type")
        }
    }
    
    whoAmI(55)        // Prints: its an integer
    whoAmI("hello")   // Prints: its a string
    whoAmI(true)      // Prints: its a boolean
}
```

## Why Use Switch Instead of If-Else?

### Compare These Two Approaches:

#### Using If-Else (Gets Messy)
```go
grade := "B"

if grade == "A" {
    fmt.Println("Excellent! 90-100%")
} else if grade == "B" {
    fmt.Println("Good! 80-89%")
} else if grade == "C" {
    fmt.Println("Average! 70-79%")
} else if grade == "D" {
    fmt.Println("Below Average! 60-69%")
} else if grade == "F" {
    fmt.Println("Failed! Below 60%")
} else {
    fmt.Println("Invalid grade")
}
```

#### Using Switch (Much Cleaner!)
```go
grade := "B"

switch grade {
case "A":
    fmt.Println("Excellent! 90-100%")
case "B":
    fmt.Println("Good! 80-89%")        // This runs
case "C":
    fmt.Println("Average! 70-79%")
case "D":
    fmt.Println("Below Average! 60-69%")
case "F":
    fmt.Println("Failed! Below 60%")
default:
    fmt.Println("Invalid grade")
}
```

## Different Switch Patterns

### 1. Simple Value Matching
```go
status := "pending"

switch status {
case "pending":
    fmt.Println("Order is being processed")
case "shipped":
    fmt.Println("Order is on the way")
case "delivered":
    fmt.Println("Order completed")
case "cancelled":
    fmt.Println("Order was cancelled")
default:
    fmt.Println("Unknown status")
}
```

### 2. Multiple Values in One Case
```go
month := "December"

switch month {
case "December", "January", "February":    // Winter months
    fmt.Println("It's winter - dress warm!")
case "March", "April", "May":              // Spring months
    fmt.Println("It's spring - flowers are blooming!")
case "June", "July", "August":             // Summer months
    fmt.Println("It's summer - time for vacation!")
case "September", "October", "November":   // Fall months
    fmt.Println("It's fall - leaves are changing!")
default:
    fmt.Println("Invalid month")
}
```

### 3. Conditionless Switch (Like If-Else Chain)
```go
score := 85
grade := ""

switch {  // No variable after switch
case score >= 90:
    grade = "A"
case score >= 80:  // This matches (85 >= 80)
    grade = "B"
case score >= 70:
    grade = "C"
case score >= 60:
    grade = "D"
default:
    grade = "F"
}

fmt.Println("Your grade is:", grade)  // Prints: Your grade is: B
```

### 4. Switch with Short Statement
```go
// Declare variable and switch on it in one line
switch day := time.Now().Weekday(); {
case day == time.Saturday || day == time.Sunday:
    fmt.Println("Weekend!")
default:
    fmt.Printf("Weekday: %v\n", day)
}
```

### 5. Type Switch (Advanced)
```go
func describe(value interface{}) {  // Accept any type
    switch v := value.(type) {      // Get actual value and type
    case int:
        fmt.Printf("Integer: %d\n", v)
    case float64:
        fmt.Printf("Float: %.2f\n", v)
    case string:
        fmt.Printf("String: %s\n", v)
    case bool:
        fmt.Printf("Boolean: %t\n", v)
    default:
        fmt.Printf("Unknown type: %T\n", v)
    }
}

describe(42)        // Integer: 42
describe(3.14)      // Float: 3.14
describe("hello")   // String: hello
describe(true)      // Boolean: true
```

## Key Differences from Other Languages

### 1. No Automatic Fallthrough
**In many languages**, cases "fall through" to the next case unless you add `break`:
```javascript
// JavaScript example (NOT Go!)
switch(day) {
    case 1:
        console.log("Monday");
        // falls through to Tuesday without break!
    case 2: 
        console.log("Tuesday");
        break;
}
```

**In Go**, each case automatically breaks - no fallthrough by default:
```go
day := 1
switch day {
case 1:
    fmt.Println("Monday")    // Prints this and stops
case 2:
    fmt.Println("Tuesday")   // This does NOT run
}
```

### 2. Explicit Fallthrough (Rarely Used)
If you want fallthrough behavior, use the `fallthrough` keyword:
```go
grade := "A"
switch grade {
case "A":
    fmt.Println("Excellent!")
    fallthrough              // Continue to next case
case "B":
    fmt.Println("Good job!")
case "C":
    fmt.Println("Not bad")
}
// For grade "A", prints:
// Excellent!
// Good job!
```

## Real-World Examples

### HTTP Status Code Handling
```go
statusCode := 404

switch statusCode {
case 200:
    fmt.Println("Success")
case 201:
    fmt.Println("Created")
case 400:
    fmt.Println("Bad Request")
case 401:
    fmt.Println("Unauthorized")
case 404:
    fmt.Println("Not Found")           // This runs
case 500:
    fmt.Println("Internal Server Error")
default:
    fmt.Printf("Unknown status code: %d\n", statusCode)
}
```

### Game Character Actions
```go
action := "attack"
playerClass := "warrior"

switch action {
case "attack":
    switch playerClass {
    case "warrior":
        fmt.Println("Swings sword for heavy damage!")
    case "mage":
        fmt.Println("Casts fireball!")
    case "archer":
        fmt.Println("Shoots precise arrow!")
    default:
        fmt.Println("Basic punch")
    }
case "defend":
    fmt.Println("Raises shield")
case "heal":
    fmt.Println("Uses potion")
default:
    fmt.Println("Invalid action")
}
```

### File Extension Processing
```go
filename := "document.pdf"
extension := strings.ToLower(filepath.Ext(filename))  // Get file extension

switch extension {
case ".jpg", ".jpeg", ".png", ".gif":
    fmt.Println("Image file")
case ".pdf":
    fmt.Println("PDF document")               // This runs
case ".doc", ".docx":
    fmt.Println("Word document")
case ".txt":
    fmt.Println("Text file")
case ".go":
    fmt.Println("Go source code")
default:
    fmt.Println("Unknown file type")
}
```

### User Permission Levels
```go
userRole := "admin"
action := "delete_user"

switch {
case userRole == "admin":
    fmt.Println("Full access granted")        // This runs
case userRole == "moderator" && action != "delete_user":
    fmt.Println("Limited access granted") 
case userRole == "user":
    fmt.Println("Read-only access")
default:
    fmt.Println("Access denied")
}
```

## When to Use Switch vs If-Else

### Use Switch When:
- Comparing **one variable** against multiple possible values
- You have **many conditions** (3+ cases)
- Values are **discrete/exact** (not ranges)
- You want **cleaner, more readable** code

### Use If-Else When:
- Comparing **different variables** in each condition
- Using **complex logical conditions** (&&, ||)
- Working with **ranges** (greater than, less than)
- Only have **1-2 conditions**

## Common Beginner Mistakes

### 1. Expecting Fallthrough
```go
// ❌ Expecting this to print multiple messages
number := 1
switch number {
case 1:
    fmt.Println("One")
case 2:
    fmt.Println("Two")    // This does NOT run for number = 1
}
// Only prints: One

// ✅ If you want multiple messages, use if-else or explicit fallthrough
```

### 2. Forgetting Default Case
```go
// ❌ What if grade is something unexpected?
grade := "X"  // Invalid grade
switch grade {
case "A":
    fmt.Println("Excellent")
case "B":
    fmt.Println("Good")
// No default case - nothing happens!
}

// ✅ Always consider a default case
switch grade {
case "A":
    fmt.Println("Excellent")
case "B":
    fmt.Println("Good")
default:
    fmt.Println("Invalid grade")
}
```

## How to Run This

```powershell
cd 7_switch
go run .
```

## Best Practices

1. **Always include a default case** unless you're certain all cases are covered
2. **Keep cases simple** - avoid complex logic in each case
3. **Use multiple values in one case** instead of duplicating code
4. **Consider conditionless switch** for complex condition chains
5. **Group related cases together** for better readability

Switch statements are like a well-organized filing cabinet - they help you quickly find and execute the right code based on specific values!
