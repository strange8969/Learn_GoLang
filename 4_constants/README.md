# Constants - Values That Never Change

This folder teaches you about constants - values that are set once and can never be modified. Think of them as permanent labels that don't change throughout your program.

## What This Teaches You

**Understanding Constants:**
- Values that are fixed at compile time (when you build the program)
- Cannot be changed once declared
- Useful for configuration values, mathematical constants, and fixed settings
- More efficient than variables because Go knows they won't change

## The Code Explained Simply

```go
package main
import "fmt"

func main() {
    // Single constant
    const name = "golang"     // This will always be "golang"
    const age = 30           // This will always be 30
    
    // Multiple constants in a group
    const (
        port = 5000          // Server will always run on port 5000
        host = "localhost"   // Server address never changes
    )
    
    fmt.Println(port, host)  // Prints: 5000 localhost
}
```

## When to Use Constants vs Variables

### Use Constants When:
- **Configuration settings**: Port numbers, API keys, database names
- **Mathematical values**: Pi, speed of light, conversion rates  
- **Fixed strings**: Error messages, status codes
- **Limits**: Maximum file size, timeout durations

### Examples:
```go
const (
    DatabaseURL    = "localhost:5432"
    MaxRetries     = 3
    TimeoutSeconds = 30
    Pi             = 3.14159
    AppVersion     = "1.2.0"
)
```

### Use Variables When:
- Values that change during program execution
- User input, calculation results, counters
- Data that comes from external sources

## Different Ways to Declare Constants

### Single Constants
```go
const companyName = "TechCorp"
const maxUsers = 1000
const isProduction = true
```

### Grouped Constants (Preferred Style)
```go
const (
    // Server configuration
    ServerPort     = 8080
    ServerHost     = "localhost"
    ServerProtocol = "https"
    
    // Database settings
    DbHost     = "db.example.com"
    DbPort     = 5432
    DbName     = "myapp"
    
    // Business rules
    MaxLoginAttempts = 5
    SessionTimeout   = 3600  // seconds
)
```

### Typed Constants (When You Need Specific Types)
```go
const (
    Username string  = "admin"      // Explicitly a string
    Port     int     = 8080         // Explicitly an integer
    Rate     float64 = 0.085        // Explicitly a 64-bit float
)
```

## Key Concepts to Remember

### 1. **Constants Must Be Known at Compile Time**
```go
// ✅ These work - values known when building program
const pi = 3.14159
const greeting = "Hello, World!"
const maxSize = 100 * 1024  // Math with constants is fine

// ❌ These don't work - values only known when program runs
const currentTime = time.Now()        // Function call
const userInput = getUserName()       // Function result
const randomNumber = rand.Intn(100)   // Random value
```

### 2. **Constants Can't Be Changed**
```go
const appName = "MyApp"
appName = "NewName"  // ❌ Compile error! Cannot change constants
```

### 3. **Constants Don't Use := Syntax**
```go
const name := "John"  // ❌ Wrong syntax
const name = "John"   // ✅ Correct syntax
```

## Real-World Examples

### Web Server Configuration
```go
const (
    // Server settings
    DefaultPort        = 8080
    MaxRequestSize     = 10 * 1024 * 1024  // 10MB
    RequestTimeout     = 30                 // seconds
    
    // API endpoints
    LoginEndpoint      = "/api/auth/login"
    UserEndpoint       = "/api/users"
    HealthEndpoint     = "/health"
    
    // Error messages
    ErrInvalidUser     = "Invalid username or password"
    ErrServerError     = "Internal server error"
    ErrNotFound        = "Resource not found"
)
```

### Game Configuration
```go
const (
    // Game settings
    PlayerStartLives   = 3
    MaxLevel          = 50
    BonusPoints       = 1000
    
    // Physics
    Gravity           = 9.8
    JumpForce         = 15.0
    MoveSpeed         = 5.0
    
    // Display
    ScreenWidth       = 1920
    ScreenHeight      = 1080
    GameTitle         = "Super Adventure Game"
)
```

### E-commerce Settings
```go
const (
    // Pricing
    TaxRate           = 0.08         // 8%
    ShippingCost      = 5.99
    FreeShippingMin   = 50.00
    
    // Business rules  
    MaxCartItems      = 100
    MaxWishlistItems  = 500
    OrderTimeout      = 15           // minutes
    
    // Status codes
    OrderPending      = "PENDING"
    OrderConfirmed    = "CONFIRMED" 
    OrderShipped      = "SHIPPED"
    OrderDelivered    = "DELIVERED"
)
```

## Benefits of Using Constants

1. **Performance**: Go can optimize constants better than variables
2. **Safety**: Can't accidentally change important values
3. **Clarity**: Makes code self-documenting
4. **Maintenance**: Easy to update configuration in one place

## Common Patterns

### Organizing Constants by Category
```go
const (
    // HTTP Status Codes
    StatusOK                  = 200
    StatusBadRequest          = 400
    StatusUnauthorized        = 401
    StatusNotFound            = 404
    StatusInternalServerError = 500
)

const (
    // File Operations
    ReadPermission  = 0644
    WritePermission = 0755
    TempDir         = "/tmp"
    LogFile         = "/var/log/app.log"
)
```

## How to Run This

```powershell
cd 4_constants
go run .
```

## What You Should See

```
5000 localhost
```

## Best Practices

1. **Group related constants** together with `const ()`
2. **Use descriptive names** that explain what the value represents
3. **Document complex constants** with comments
4. **Use ALL_CAPS** for constants that come from external sources (optional)
5. **Keep constants close** to the code that uses them

Constants are like permanent road signs in your code - they provide fixed reference points that never change, making your program more reliable and easier to understand!
