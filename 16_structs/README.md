# Structs - Building Custom Data Types in Go

This folder teaches you about structs - Go's way of grouping related data together to create your own custom types. Think of structs as blueprints for creating objects that hold multiple pieces of related information, like a person's name, age, and address all bundled together.

## What This Teaches You

**Struct Fundamentals:**
- Creating custom types with struct
- Field access with dot notation
- Struct initialization patterns
- Anonymous structs for quick data grouping
- Embedded structs (composition over inheritance)
- Methods on structs (receiver functions)
- Struct tags for metadata
- Comparing structs and struct equality

## The Code Explained Simply

```go
package main
import "fmt"

// Define a struct type
type Person struct {
    Name string
    Age  int
    City string
}

func main() {
    // Create struct instances
    var p1 Person
    p1.Name = "Alice"
    p1.Age = 30
    p1.City = "New York"
    
    // Struct literal initialization
    p2 := Person{
        Name: "Bob",
        Age:  25,
        City: "Los Angeles",
    }
    
    // Short initialization (field order matters)
    p3 := Person{"Charlie", 35, "Chicago"}
    
    // Print struct values
    fmt.Printf("Person 1: %+v\n", p1)  // %+v shows field names
    fmt.Printf("Person 2: %s is %d years old\n", p2.Name, p2.Age)
    fmt.Printf("Person 3 lives in %s\n", p3.City)
}
```

## Struct Definition and Initialization

### 1. **Basic Struct Definition**
```go
// Simple struct
type Point struct {
    X int
    Y int
}

// Struct with different field types
type Student struct {
    ID       int
    Name     string
    GPA      float64
    IsActive bool
    Courses  []string
}

// Struct with nested structs
type Address struct {
    Street  string
    City    string
    State   string
    ZipCode string
}

type Employee struct {
    ID      int
    Name    string
    Email   string
    Address Address  // Nested struct
    Salary  float64
}

func main() {
    // Zero value initialization
    var p Point  // X: 0, Y: 0 (zero values)
    fmt.Printf("Zero point: %+v\n", p)  // {X:0 Y:0}
    
    // Field assignment
    p.X = 10
    p.Y = 20
    fmt.Printf("Assigned point: %+v\n", p)  // {X:10 Y:20}
    
    // Struct literal with field names (recommended)
    student := Student{
        ID:       12345,
        Name:     "Alice Johnson",
        GPA:      3.8,
        IsActive: true,
        Courses:  []string{"Math", "Physics", "Chemistry"},
    }
    fmt.Printf("Student: %+v\n", student)
    
    // Nested struct initialization
    emp := Employee{
        ID:    1001,
        Name:  "John Doe",
        Email: "john@company.com",
        Address: Address{
            Street:  "123 Main St",
            City:    "Boston",
            State:   "MA",
            ZipCode: "02101",
        },
        Salary: 75000.0,
    }
    
    // Access nested fields
    fmt.Printf("Employee %s works at %s, %s\n", 
        emp.Name, emp.Address.City, emp.Address.State)
}
```

### 2. **Different Initialization Patterns**
```go
type Rectangle struct {
    Width  float64
    Height float64
    Color  string
}

func main() {
    // Method 1: Zero value then assign
    var rect1 Rectangle
    rect1.Width = 10.0
    rect1.Height = 5.0
    rect1.Color = "red"
    
    // Method 2: Struct literal with field names (preferred)
    rect2 := Rectangle{
        Width:  15.0,
        Height: 8.0,
        Color:  "blue",
    }
    
    // Method 3: Struct literal without field names (order matters!)
    rect3 := Rectangle{20.0, 12.0, "green"}  // Width, Height, Color
    
    // Method 4: Partial initialization (missing fields get zero values)
    rect4 := Rectangle{
        Width: 25.0,
        Color: "yellow",
        // Height gets zero value (0.0)
    }
    
    // Method 5: Using new() keyword
    rect5 := new(Rectangle)  // Returns *Rectangle (pointer)
    rect5.Width = 30.0
    rect5.Height = 18.0
    rect5.Color = "purple"
    
    // Method 6: Constructor pattern
    rect6 := NewRectangle(35.0, 22.0, "orange")
    
    fmt.Printf("rect1: %+v\n", rect1)
    fmt.Printf("rect2: %+v\n", rect2)
    fmt.Printf("rect3: %+v\n", rect3)
    fmt.Printf("rect4: %+v\n", rect4)
    fmt.Printf("rect5: %+v\n", *rect5)  // Dereference pointer
    fmt.Printf("rect6: %+v\n", rect6)
}

// Constructor function pattern
func NewRectangle(width, height float64, color string) Rectangle {
    return Rectangle{
        Width:  width,
        Height: height,
        Color:  color,
    }
}

// Constructor returning pointer (more common)
func NewRectanglePtr(width, height float64, color string) *Rectangle {
    return &Rectangle{
        Width:  width,
        Height: height,
        Color:  color,
    }
}
```

### 3. **Anonymous Structs**
```go
func main() {
    // Anonymous struct for quick data grouping
    config := struct {
        Host     string
        Port     int
        Database string
    }{
        Host:     "localhost",
        Port:     5432,
        Database: "myapp",
    }
    
    fmt.Printf("Config: %+v\n", config)
    
    // Anonymous struct in function
    processConfig(struct {
        APIKey    string
        RateLimit int
    }{
        APIKey:    "abc123",
        RateLimit: 100,
    })
    
    // Slice of anonymous structs
    users := []struct {
        Name  string
        Score int
    }{
        {"Alice", 95},
        {"Bob", 87},
        {"Charlie", 92},
    }
    
    fmt.Println("User scores:")
    for _, user := range users {
        fmt.Printf("  %s: %d\n", user.Name, user.Score)
    }
    
    // Map with anonymous struct values
    inventory := map[string]struct {
        Quantity int
        Price    float64
    }{
        "laptop": {Quantity: 10, Price: 999.99},
        "mouse":  {Quantity: 50, Price: 29.99},
        "keyboard": {Quantity: 25, Price: 79.99},
    }
    
    fmt.Println("Inventory:")
    for item, details := range inventory {
        fmt.Printf("  %s: %d units at $%.2f each\n", 
            item, details.Quantity, details.Price)
    }
}

func processConfig(cfg struct {
    APIKey    string
    RateLimit int
}) {
    fmt.Printf("Processing config: API=%s, Limit=%d\n", 
        cfg.APIKey, cfg.RateLimit)
}
```

## Methods on Structs

### 1. **Defining Methods**
```go
type Circle struct {
    Radius float64
    Color  string
}

// Method with value receiver
func (c Circle) Area() float64 {
    return 3.14159 * c.Radius * c.Radius
}

// Method with value receiver (read-only)
func (c Circle) String() string {
    return fmt.Sprintf("Circle(radius: %.1f, color: %s)", c.Radius, c.Color)
}

// Method with pointer receiver (can modify)
func (c *Circle) SetColor(color string) {
    c.Color = color
}

// Method with pointer receiver (can modify)
func (c *Circle) Scale(factor float64) {
    c.Radius *= factor
}

// Method returning multiple values
func (c Circle) Properties() (float64, float64, string) {
    area := c.Area()
    circumference := 2 * 3.14159 * c.Radius
    return area, circumference, c.Color
}

func main() {
    circle := Circle{
        Radius: 5.0,
        Color:  "red",
    }
    
    // Call methods
    fmt.Printf("Circle: %s\n", circle.String())
    fmt.Printf("Area: %.2f\n", circle.Area())
    
    // Methods with pointer receivers
    circle.SetColor("blue")
    fmt.Printf("After color change: %s\n", circle.String())
    
    circle.Scale(2.0)
    fmt.Printf("After scaling: %s\n", circle.String())
    fmt.Printf("New area: %.2f\n", circle.Area())
    
    // Multiple return values
    area, circumference, color := circle.Properties()
    fmt.Printf("Properties - Area: %.2f, Circumference: %.2f, Color: %s\n", 
        area, circumference, color)
}
```

### 2. **Value vs Pointer Receivers**
```go
type BankAccount struct {
    AccountNumber string
    Balance       float64
    Owner         string
}

// Value receiver - works with copies, can't modify original
func (ba BankAccount) GetBalance() float64 {
    return ba.Balance  // Read-only operation
}

func (ba BankAccount) GetOwner() string {
    return ba.Owner  // Read-only operation
}

// Pointer receiver - works with original, can modify
func (ba *BankAccount) Deposit(amount float64) {
    if amount > 0 {
        ba.Balance += amount  // Modifies original
    }
}

func (ba *BankAccount) Withdraw(amount float64) bool {
    if amount > 0 && ba.Balance >= amount {
        ba.Balance -= amount  // Modifies original
        return true
    }
    return false
}

// Pointer receiver for consistency (even for read-only)
func (ba *BankAccount) String() string {
    return fmt.Sprintf("Account %s: %s has $%.2f", 
        ba.AccountNumber, ba.Owner, ba.Balance)
}

func main() {
    // Create account
    account := BankAccount{
        AccountNumber: "12345",
        Balance:       1000.0,
        Owner:         "Alice Johnson",
    }
    
    fmt.Printf("Initial: %s\n", account.String())
    
    // Deposit money
    account.Deposit(250.0)
    fmt.Printf("After deposit: Balance = $%.2f\n", account.GetBalance())
    
    // Withdraw money
    success := account.Withdraw(100.0)
    if success {
        fmt.Printf("Withdrawal successful. New balance: $%.2f\n", account.GetBalance())
    }
    
    // Try to withdraw more than available
    success = account.Withdraw(2000.0)
    if !success {
        fmt.Println("Withdrawal failed: insufficient funds")
    }
    
    fmt.Printf("Final: %s\n", account.String())
}
```

### 3. **Methods vs Functions**
```go
type Vector struct {
    X, Y float64
}

// Method on Vector struct
func (v Vector) Length() float64 {
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func (v *Vector) Scale(factor float64) {
    v.X *= factor
    v.Y *= factor
}

// Regular function that works with Vector
func VectorLength(v Vector) float64 {
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func ScaleVector(v *Vector, factor float64) {
    v.X *= factor
    v.Y *= factor
}

// Function that takes two vectors
func AddVectors(v1, v2 Vector) Vector {
    return Vector{
        X: v1.X + v2.X,
        Y: v1.Y + v2.Y,
    }
}

// Method that adds another vector to this one
func (v *Vector) Add(other Vector) {
    v.X += other.X
    v.Y += other.Y
}

func main() {
    v1 := Vector{X: 3.0, Y: 4.0}
    v2 := Vector{X: 1.0, Y: 2.0}
    
    // Using methods
    fmt.Printf("v1 length (method): %.2f\n", v1.Length())
    v1.Scale(2.0)
    fmt.Printf("v1 after scaling: %+v\n", v1)
    
    // Using functions
    v3 := Vector{X: 3.0, Y: 4.0}
    fmt.Printf("v3 length (function): %.2f\n", VectorLength(v3))
    ScaleVector(&v3, 2.0)
    fmt.Printf("v3 after scaling: %+v\n", v3)
    
    // Vector addition
    v4 := AddVectors(v1, v2)  // Function approach
    fmt.Printf("v1 + v2 (function): %+v\n", v4)
    
    v1.Add(v2)  // Method approach
    fmt.Printf("v1 after adding v2 (method): %+v\n", v1)
}
```

## Struct Embedding (Composition)

### 1. **Basic Embedding**
```go
// Base struct
type Animal struct {
    Name    string
    Species string
    Age     int
}

func (a Animal) Speak() {
    fmt.Printf("%s makes a sound\n", a.Name)
}

func (a Animal) Info() string {
    return fmt.Sprintf("%s is a %d-year-old %s", a.Name, a.Age, a.Species)
}

// Embedded struct - Dog "has-a" Animal
type Dog struct {
    Animal  // Embedded struct (anonymous field)
    Breed   string
    IsGoodDog bool
}

// Dog can override Animal methods
func (d Dog) Speak() {
    fmt.Printf("%s barks: Woof woof!\n", d.Name)  // Can access embedded fields directly
}

func (d Dog) Fetch() {
    fmt.Printf("%s is fetching the ball!\n", d.Name)
}

// Cat with embedded Animal
type Cat struct {
    Animal
    IndoorOnly bool
}

func (c Cat) Speak() {
    fmt.Printf("%s meows: Meow meow!\n", c.Name)
}

func (c Cat) Purr() {
    fmt.Printf("%s is purring contentedly\n", c.Name)
}

func main() {
    // Create Dog with embedded Animal
    dog := Dog{
        Animal: Animal{
            Name:    "Buddy",
            Species: "Dog",
            Age:     3,
        },
        Breed:     "Golden Retriever", 
        IsGoodDog: true,
    }
    
    // Access embedded fields directly
    fmt.Printf("Dog name: %s\n", dog.Name)        // From embedded Animal
    fmt.Printf("Dog breed: %s\n", dog.Breed)      // From Dog itself
    fmt.Printf("Dog info: %s\n", dog.Info())      // Method from embedded Animal
    
    // Call overridden method
    dog.Speak()  // Uses Dog's version, not Animal's
    
    // Call Dog-specific method
    dog.Fetch()
    
    // Create Cat
    cat := Cat{
        Animal: Animal{
            Name:    "Whiskers",
            Species: "Cat", 
            Age:     2,
        },
        IndoorOnly: true,
    }
    
    fmt.Printf("Cat info: %s\n", cat.Info())  // From embedded Animal
    cat.Speak()  // Uses Cat's version
    cat.Purr()   // Cat-specific method
    
    // Can access embedded struct explicitly too
    fmt.Printf("Cat's animal info: %s\n", cat.Animal.Info())
    cat.Animal.Speak()  // Call original Animal method
}
```

### 2. **Multiple Embedding**
```go
// Contact information
type ContactInfo struct {
    Email string
    Phone string
}

func (c ContactInfo) SendEmail(message string) {
    fmt.Printf("Sending email to %s: %s\n", c.Email, message)
}

// Address information  
type Address struct {
    Street  string
    City    string
    State   string
    ZipCode string
}

func (a Address) FullAddress() string {
    return fmt.Sprintf("%s, %s, %s %s", a.Street, a.City, a.State, a.ZipCode)
}

// Person with multiple embedded structs
type Person struct {
    Name        string
    Age         int
    ContactInfo  // Embedded
    Address      // Embedded
}

// Method on Person
func (p Person) Introduce() {
    fmt.Printf("Hi, I'm %s, %d years old\n", p.Name, p.Age)
    fmt.Printf("I live at %s\n", p.FullAddress())  // Method from embedded Address
    fmt.Printf("Contact me at %s\n", p.Email)       // Field from embedded ContactInfo
}

func main() {
    person := Person{
        Name: "Sarah Johnson",
        Age:  28,
        ContactInfo: ContactInfo{
            Email: "sarah@example.com",
            Phone: "555-1234",
        },
        Address: Address{
            Street:  "123 Oak Street",
            City:    "Portland",
            State:   "OR",
            ZipCode: "97201",
        },
    }
    
    // Use methods from embedded structs
    person.Introduce()  // Person's method
    person.SendEmail("Hello from Go!")  // ContactInfo's method
    
    // Access fields from embedded structs directly
    fmt.Printf("Phone: %s\n", person.Phone)        // From ContactInfo
    fmt.Printf("City: %s\n", person.City)          // From Address
}
```

### 3. **Interface Satisfaction through Embedding**
```go
// Interfaces
type Writer interface {
    Write(data []byte) (int, error)
}

type Reader interface {
    Read(data []byte) (int, error)
}

type ReadWriter interface {
    Reader
    Writer
}

// Basic file-like struct
type FileHandle struct {
    filename string
    content  []byte
    position int
}

func (f *FileHandle) Read(data []byte) (int, error) {
    if f.position >= len(f.content) {
        return 0, fmt.Errorf("EOF")
    }
    
    n := copy(data, f.content[f.position:])
    f.position += n
    return n, nil
}

func (f *FileHandle) Write(data []byte) (int, error) {
    f.content = append(f.content, data...)
    return len(data), nil
}

// BufferedFile embeds FileHandle and adds buffering
type BufferedFile struct {
    *FileHandle  // Embedded pointer (composition)
    bufferSize   int
}

func NewBufferedFile(filename string, bufferSize int) *BufferedFile {
    return &BufferedFile{
        FileHandle: &FileHandle{
            filename: filename,
            content:  make([]byte, 0),
            position: 0,
        },
        bufferSize: bufferSize,
    }
}

// BufferedFile automatically satisfies ReadWriter interface
// through embedded FileHandle

func processReadWriter(rw ReadWriter) {
    // Write some data
    data := []byte("Hello, World!")
    n, err := rw.Write(data)
    if err != nil {
        fmt.Printf("Write error: %v\n", err)
        return
    }
    fmt.Printf("Wrote %d bytes\n", n)
    
    // Read it back
    buffer := make([]byte, 20)
    n, err = rw.Read(buffer)
    if err != nil {
        fmt.Printf("Read error: %v\n", err)
        return
    }
    fmt.Printf("Read %d bytes: %s\n", n, string(buffer[:n]))
}

func main() {
    // Create buffered file
    bf := NewBufferedFile("test.txt", 1024)
    
    // Can be used as ReadWriter interface
    processReadWriter(bf)
    
    // Can access embedded methods directly
    bf.Write([]byte(" Additional content"))
    
    // Reset position to read from beginning
    bf.position = 0
    
    buffer := make([]byte, 100)
    n, _ := bf.Read(buffer)
    fmt.Printf("Full content: %s\n", string(buffer[:n]))
}
```

## Struct Tags and Reflection

### 1. **JSON Tags**
```go
import (
    "encoding/json"
    "fmt"
)

type User struct {
    ID       int    `json:"id"`
    Name     string `json:"name"`
    Email    string `json:"email"`
    Password string `json:"-"`          // Never include in JSON
    Age      int    `json:"age,omitempty"` // Omit if zero value
    IsActive bool   `json:"is_active"`
}

type Product struct {
    ID          int     `json:"id"`
    Name        string  `json:"name"`
    Price       float64 `json:"price"`
    Description string  `json:"description,omitempty"`
    InStock     bool    `json:"in_stock"`
    Categories  []string `json:"categories"`
}

func main() {
    user := User{
        ID:       123,
        Name:     "Alice Johnson",
        Email:    "alice@example.com",
        Password: "secret123",  // This won't appear in JSON
        Age:      0,            // This will be omitted due to omitempty
        IsActive: true,
    }
    
    // Convert to JSON
    jsonData, err := json.MarshalIndent(user, "", "  ")
    if err != nil {
        fmt.Printf("JSON marshal error: %v\n", err)
        return
    }
    
    fmt.Printf("User JSON:\n%s\n", jsonData)
    
    // Parse JSON back to struct
    jsonString := `{
        "id": 456,
        "name": "Bob Smith",
        "email": "bob@example.com", 
        "age": 30,
        "is_active": false
    }`
    
    var parsedUser User
    err = json.Unmarshal([]byte(jsonString), &parsedUser)
    if err != nil {
        fmt.Printf("JSON unmarshal error: %v\n", err)
        return
    }
    
    fmt.Printf("Parsed user: %+v\n", parsedUser)
    
    // Product example
    product := Product{
        ID:         1001,
        Name:       "Laptop",
        Price:      999.99,
        InStock:    true,
        Categories: []string{"Electronics", "Computers"},
    }
    
    productJSON, _ := json.MarshalIndent(product, "", "  ")
    fmt.Printf("\nProduct JSON:\n%s\n", productJSON)
}
```

### 2. **Custom Tags for Validation**
```go
import (
    "fmt"
    "reflect"
    "strings"
)

type ValidationRule struct {
    Required bool
    MinLen   int
    MaxLen   int
    Pattern  string
}

type RegisterForm struct {
    Username string `validate:"required,minlen:3,maxlen:20"`
    Email    string `validate:"required,pattern:@"`
    Password string `validate:"required,minlen:8"`
    Age      int    `validate:"required"`
    Bio      string `validate:"maxlen:500"`
}

func validateStruct(s interface{}) []string {
    var errors []string
    
    val := reflect.ValueOf(s)
    typ := reflect.TypeOf(s)
    
    for i := 0; i < val.NumField(); i++ {
        field := val.Field(i)
        fieldType := typ.Field(i)
        
        tag := fieldType.Tag.Get("validate")
        if tag == "" {
            continue
        }
        
        fieldName := fieldType.Name
        fieldValue := field.Interface()
        
        // Parse validation rules
        rules := strings.Split(tag, ",")
        for _, rule := range rules {
            err := validateRule(fieldName, fieldValue, rule)
            if err != "" {
                errors = append(errors, err)
            }
        }
    }
    
    return errors
}

func validateRule(fieldName string, value interface{}, rule string) string {
    switch {
    case rule == "required":
        if isZeroValue(value) {
            return fmt.Sprintf("%s is required", fieldName)
        }
    case strings.HasPrefix(rule, "minlen:"):
        minLen := 0
        fmt.Sscanf(rule, "minlen:%d", &minLen)
        if str, ok := value.(string); ok && len(str) < minLen {
            return fmt.Sprintf("%s must be at least %d characters", fieldName, minLen)
        }
    case strings.HasPrefix(rule, "maxlen:"):
        maxLen := 0
        fmt.Sscanf(rule, "maxlen:%d", &maxLen)
        if str, ok := value.(string); ok && len(str) > maxLen {
            return fmt.Sprintf("%s must be at most %d characters", fieldName, maxLen)
        }
    case strings.HasPrefix(rule, "pattern:"):
        pattern := strings.TrimPrefix(rule, "pattern:")
        if str, ok := value.(string); ok && !strings.Contains(str, pattern) {
            return fmt.Sprintf("%s must contain '%s'", fieldName, pattern)
        }
    }
    return ""
}

func isZeroValue(value interface{}) bool {
    v := reflect.ValueOf(value)
    return v.IsZero()
}

func main() {
    // Valid form
    validForm := RegisterForm{
        Username: "alice123",
        Email:    "alice@example.com",
        Password: "secretpassword",
        Age:      25,
        Bio:      "I love programming in Go!",
    }
    
    errors := validateStruct(validForm)
    if len(errors) == 0 {
        fmt.Println("Valid form!")
    } else {
        fmt.Println("Validation errors:")
        for _, err := range errors {
            fmt.Printf("  - %s\n", err)
        }
    }
    
    // Invalid form
    invalidForm := RegisterForm{
        Username: "al",  // Too short
        Email:    "alice.com",  // No @
        Password: "123",  // Too short
        // Age: 0,  // Zero value, but required
        Bio: strings.Repeat("a", 600),  // Too long
    }
    
    fmt.Println("\nTesting invalid form:")
    errors = validateStruct(invalidForm)
    for _, err := range errors {
        fmt.Printf("  - %s\n", err)
    }
}
```

## Struct Comparison and Copying

### 1. **Struct Equality**
```go
type Point struct {
    X, Y int
}

type Person struct {
    Name string
    Age  int
}

type Container struct {
    Items []string  // Slice - not comparable
}

func main() {
    // Comparable structs (all fields are comparable)
    p1 := Point{X: 1, Y: 2}
    p2 := Point{X: 1, Y: 2}
    p3 := Point{X: 3, Y: 4}
    
    fmt.Printf("p1 == p2: %t\n", p1 == p2)  // true
    fmt.Printf("p1 == p3: %t\n", p1 == p3)  // false
    
    person1 := Person{Name: "Alice", Age: 30}
    person2 := Person{Name: "Alice", Age: 30}
    person3 := Person{Name: "Bob", Age: 30}
    
    fmt.Printf("person1 == person2: %t\n", person1 == person2)  // true
    fmt.Printf("person1 == person3: %t\n", person1 == person3)  // false
    
    // Non-comparable structs (contain slices, maps, or functions)
    container1 := Container{Items: []string{"a", "b"}}
    container2 := Container{Items: []string{"a", "b"}}
    
    // This would cause compile error:
    // fmt.Printf("container1 == container2: %t\n", container1 == container2)
    
    // Must compare manually
    fmt.Printf("Containers equal: %t\n", containersEqual(container1, container2))
}

func containersEqual(c1, c2 Container) bool {
    if len(c1.Items) != len(c2.Items) {
        return false
    }
    for i, item := range c1.Items {
        if item != c2.Items[i] {
            return false
        }
    }
    return true
}
```

### 2. **Copying Structs**
```go
type Address struct {
    Street  string
    City    string
    State   string
}

type Person struct {
    Name    string
    Age     int
    Address Address
    Friends []string  // Reference type
}

func main() {
    original := Person{
        Name: "Alice",
        Age:  30,
        Address: Address{
            Street: "123 Main St",
            City:   "Boston", 
            State:  "MA",
        },
        Friends: []string{"Bob", "Charlie"},
    }
    
    // Shallow copy - copies struct but shares slice
    copy1 := original
    copy1.Name = "Alice Copy"
    copy1.Address.City = "New York"  // This changes the copy only
    copy1.Friends[0] = "David"       // This changes BOTH original and copy!
    
    fmt.Printf("Original: %+v\n", original)
    fmt.Printf("Copy1: %+v\n", copy1)
    
    // Deep copy function
    copy2 := deepCopyPerson(original)
    copy2.Name = "Alice Deep Copy"
    copy2.Friends[0] = "Eve"  // This only changes copy2
    
    fmt.Printf("\nAfter deep copy:")
    fmt.Printf("Original: %+v\n", original)
    fmt.Printf("Copy2: %+v\n", copy2)
}

func deepCopyPerson(p Person) Person {
    // Copy the struct
    copy := p
    
    // Deep copy the slice
    copy.Friends = make([]string, len(p.Friends))
    for i, friend := range p.Friends {
        copy.Friends[i] = friend
    }
    
    return copy
}

// Generic deep copy using reflection (more complex)
func deepCopyWithReflection(original Person) Person {
    // For production code, consider using a library like:
    // - github.com/mohae/deepcopy
    // - github.com/jinzhu/copier
    
    copy := Person{
        Name:    original.Name,
        Age:     original.Age,
        Address: original.Address,  // Address is copiable
    }
    
    // Deep copy Friends slice
    if original.Friends != nil {
        copy.Friends = make([]string, len(original.Friends))
        copy(copy.Friends, original.Friends)
    }
    
    return copy
}
```

## Advanced Struct Patterns

### 1. **Builder Pattern**
```go
type DatabaseConfig struct {
    Host     string
    Port     int
    Database string
    Username string
    Password string
    SSL      bool
    Timeout  int
}

type DatabaseConfigBuilder struct {
    config DatabaseConfig
}

func NewDatabaseConfigBuilder() *DatabaseConfigBuilder {
    return &DatabaseConfigBuilder{
        config: DatabaseConfig{
            Host:    "localhost",
            Port:    5432,
            SSL:     false,
            Timeout: 30,
        },
    }
}

func (b *DatabaseConfigBuilder) Host(host string) *DatabaseConfigBuilder {
    b.config.Host = host
    return b
}

func (b *DatabaseConfigBuilder) Port(port int) *DatabaseConfigBuilder {
    b.config.Port = port
    return b
}

func (b *DatabaseConfigBuilder) Database(database string) *DatabaseConfigBuilder {
    b.config.Database = database
    return b
}

func (b *DatabaseConfigBuilder) Username(username string) *DatabaseConfigBuilder {
    b.config.Username = username
    return b
}

func (b *DatabaseConfigBuilder) Password(password string) *DatabaseConfigBuilder {
    b.config.Password = password
    return b
}

func (b *DatabaseConfigBuilder) SSL(ssl bool) *DatabaseConfigBuilder {
    b.config.SSL = ssl
    return b
}

func (b *DatabaseConfigBuilder) Timeout(timeout int) *DatabaseConfigBuilder {
    b.config.Timeout = timeout
    return b
}

func (b *DatabaseConfigBuilder) Build() DatabaseConfig {
    return b.config
}

func main() {
    // Build configuration fluently
    config := NewDatabaseConfigBuilder().
        Host("production.db.company.com").
        Port(5432).
        Database("myapp_production").
        Username("app_user").
        Password("secret123").
        SSL(true).
        Timeout(60).
        Build()
    
    fmt.Printf("Database config: %+v\n", config)
    
    // Another configuration with defaults
    devConfig := NewDatabaseConfigBuilder().
        Database("myapp_dev").
        Username("dev_user").
        Build()
    
    fmt.Printf("Dev config: %+v\n", devConfig)
}
```

### 2. **Option Pattern**
```go
type Server struct {
    Host     string
    Port     int
    Timeout  int
    MaxConns int
    TLS      bool
}

type ServerOption func(*Server)

func WithHost(host string) ServerOption {
    return func(s *Server) {
        s.Host = host
    }
}

func WithPort(port int) ServerOption {
    return func(s *Server) {
        s.Port = port
    }
}

func WithTimeout(timeout int) ServerOption {
    return func(s *Server) {
        s.Timeout = timeout
    }
}

func WithMaxConnections(maxConns int) ServerOption {
    return func(s *Server) {
        s.MaxConns = maxConns
    }
}

func WithTLS(tls bool) ServerOption {
    return func(s *Server) {
        s.TLS = tls
    }
}

func NewServer(options ...ServerOption) *Server {
    // Default configuration
    server := &Server{
        Host:     "localhost",
        Port:     8080,
        Timeout:  30,
        MaxConns: 100,
        TLS:      false,
    }
    
    // Apply options
    for _, option := range options {
        option(server)
    }
    
    return server
}

func main() {
    // Create server with custom options
    server1 := NewServer(
        WithHost("0.0.0.0"),
        WithPort(443),
        WithTLS(true),
        WithMaxConnections(1000),
    )
    
    fmt.Printf("Server 1: %+v\n", server1)
    
    // Create server with minimal options
    server2 := NewServer(
        WithPort(3000),
    )
    
    fmt.Printf("Server 2: %+v\n", server2)
}
```

### 3. **State Machine Pattern**
```go
type OrderState int

const (
    OrderPending OrderState = iota
    OrderConfirmed
    OrderShipped
    OrderDelivered
    OrderCancelled
)

func (os OrderState) String() string {
    switch os {
    case OrderPending:
        return "Pending"
    case OrderConfirmed:
        return "Confirmed"
    case OrderShipped:
        return "Shipped"
    case OrderDelivered:
        return "Delivered"
    case OrderCancelled:
        return "Cancelled"
    default:
        return "Unknown"
    }
}

type Order struct {
    ID       string
    Items    []string
    Total    float64
    State    OrderState
    Customer string
}

func (o *Order) Confirm() error {
    if o.State != OrderPending {
        return fmt.Errorf("cannot confirm order in state %s", o.State)
    }
    o.State = OrderConfirmed
    return nil
}

func (o *Order) Ship() error {
    if o.State != OrderConfirmed {
        return fmt.Errorf("cannot ship order in state %s", o.State)
    }
    o.State = OrderShipped
    return nil
}

func (o *Order) Deliver() error {
    if o.State != OrderShipped {
        return fmt.Errorf("cannot deliver order in state %s", o.State)
    }
    o.State = OrderDelivered
    return nil
}

func (o *Order) Cancel() error {
    if o.State == OrderDelivered {
        return fmt.Errorf("cannot cancel delivered order")
    }
    o.State = OrderCancelled
    return nil
}

func (o *Order) Status() string {
    return fmt.Sprintf("Order %s (%s): %s - $%.2f", 
        o.ID, o.Customer, o.State, o.Total)
}

func main() {
    order := &Order{
        ID:       "ORD-001",
        Items:    []string{"Laptop", "Mouse"},
        Total:    1029.98,
        State:    OrderPending,
        Customer: "Alice Johnson",
    }
    
    fmt.Println(order.Status())
    
    // Valid state transitions
    err := order.Confirm()
    if err == nil {
        fmt.Println("Order confirmed:", order.Status())
    }
    
    err = order.Ship()
    if err == nil {
        fmt.Println("Order shipped:", order.Status())
    }
    
    err = order.Deliver()
    if err == nil {
        fmt.Println("Order delivered:", order.Status())
    }
    
    // Invalid state transition
    err = order.Cancel()
    if err != nil {
        fmt.Printf("Cannot cancel: %v\n", err)
    }
}
```

## Performance Considerations

### 1. **Struct Size and Memory Layout**
```go
// Poor memory layout - lots of padding
type BadStruct struct {
    Flag1 bool    // 1 byte
    Number int64  // 8 bytes (7 bytes padding before this)
    Flag2 bool    // 1 byte  
    Pointer *int  // 8 bytes (7 bytes padding before this)
}

// Good memory layout - minimal padding
type GoodStruct struct {
    Number  int64  // 8 bytes
    Pointer *int   // 8 bytes
    Flag1   bool   // 1 byte
    Flag2   bool   // 1 byte (6 bytes padding at end)
}

func main() {
    fmt.Printf("BadStruct size: %d bytes\n", unsafe.Sizeof(BadStruct{}))   // 32 bytes
    fmt.Printf("GoodStruct size: %d bytes\n", unsafe.Sizeof(GoodStruct{})) // 24 bytes
    
    // Group related fields together for better cache performance
}
```

### 2. **Value vs Pointer Performance**
```go
type SmallStruct struct {
    ID   int
    Name string
}

type LargeStruct struct {
    Data [1000]int
    Name string
}

func processSmallByValue(s SmallStruct) {
    // Small copy, good cache locality
}

func processSmallByPointer(s *SmallStruct) {
    // Extra indirection, but no copy
}

func processLargeByValue(s LargeStruct) {
    // Expensive copy - avoid!
}

func processLargeByPointer(s *LargeStruct) {
    // Only copies pointer - much better
}

func main() {
    small := SmallStruct{ID: 1, Name: "test"}
    large := LargeStruct{Name: "test"}
    
    // For small structs, both approaches are reasonable
    processSmallByValue(small)
    processSmallByPointer(&small)
    
    // For large structs, always use pointers
    // processLargeByValue(large)     // Don't do this!
    processLargeByPointer(&large)     // Do this instead
}
```

## How to Run This

```powershell
cd 16_structs
go run .
```

## Key Takeaways

1. **Structs group related data** - create custom types that model real-world entities
2. **Use struct literals with field names** - more readable and maintainable
3. **Pointer receivers for methods** - when you need to modify the struct or it's large
4. **Embedding enables composition** - prefer composition over inheritance
5. **Struct tags add metadata** - useful for JSON, validation, and other serialization
6. **Consider memory layout** - order fields by size for optimal memory usage
7. **Structs are comparable** - if all fields are comparable types
8. **Deep vs shallow copying** - be careful with reference types in structs

Structs are like custom containers that let you bundle related information together - think of them as your own custom data types that perfectly model the things your program needs to work with, whether that's a user, a configuration, or a complex business object!
