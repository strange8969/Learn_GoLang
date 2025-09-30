# Interfaces - Defining Behavior Contracts in Go

This folder teaches you about interfaces - Go's powerful way of defining what something can do rather than what it is. Think of interfaces as contracts that say "if you can do these things, then you can be used anywhere this interface is expected." This enables incredible flexibility and loose coupling in your code.

## What This Teaches You

**Interface Fundamentals:**
- Defining interfaces to specify behavior
- Implicit interface satisfaction (no "implements" keyword)
- Empty interface and type assertions  
- Interface composition (embedding interfaces)
- Type switches for handling different types
- Common interface patterns in Go
- Interface segregation and best practices

## The Code Explained Simply

```go
package main
import "fmt"

// Define an interface - a contract of behavior
type Writer interface {
    Write([]byte) (int, error)  // Any type with this method satisfies Writer
}

// Struct that satisfies Writer interface
type FileWriter struct {
    filename string
}

func (fw FileWriter) Write(data []byte) (int, error) {
    fmt.Printf("Writing to file %s: %s\n", fw.filename, string(data))
    return len(data), nil  // Simulate successful write
}

// Another struct that satisfies Writer interface
type ConsoleWriter struct{}

func (cw ConsoleWriter) Write(data []byte) (int, error) {
    fmt.Printf("Console output: %s\n", string(data))
    return len(data), nil
}

// Function that accepts any Writer
func writeMessage(w Writer, message string) {
    w.Write([]byte(message))  // Works with any type that implements Write
}

func main() {
    // Both types can be used as Writer interface
    fileWriter := FileWriter{filename: "output.txt"}
    consoleWriter := ConsoleWriter{}
    
    // Same function works with different implementations
    writeMessage(fileWriter, "Hello, file!")      // Uses FileWriter.Write
    writeMessage(consoleWriter, "Hello, console!") // Uses ConsoleWriter.Write
}
```

## Basic Interface Concepts

### 1. **Interface Definition and Satisfaction**
```go
// Shape interface defines what a shape can do
type Shape interface {
    Area() float64
    Perimeter() float64
}

// Circle satisfies Shape interface
type Circle struct {
    Radius float64
}

func (c Circle) Area() float64 {
    return 3.14159 * c.Radius * c.Radius
}

func (c Circle) Perimeter() float64 {
    return 2 * 3.14159 * c.Radius
}

// Rectangle satisfies Shape interface
type Rectangle struct {
    Width, Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

func (r Rectangle) Perimeter() float64 {
    return 2 * (r.Width + r.Height)
}

// Triangle also satisfies Shape interface
type Triangle struct {
    Base, Height, Side1, Side2 float64
}

func (t Triangle) Area() float64 {
    return 0.5 * t.Base * t.Height
}

func (t Triangle) Perimeter() float64 {
    return t.Base + t.Side1 + t.Side2
}

// Functions that work with any Shape
func printShapeInfo(s Shape) {
    fmt.Printf("Shape area: %.2f, perimeter: %.2f\n", s.Area(), s.Perimeter())
}

func totalArea(shapes []Shape) float64 {
    total := 0.0
    for _, shape := range shapes {
        total += shape.Area()
    }
    return total
}

func main() {
    // Create different shapes
    circle := Circle{Radius: 5}
    rectangle := Rectangle{Width: 10, Height: 6}
    triangle := Triangle{Base: 8, Height: 6, Side1: 7, Side2: 9}
    
    // All can be treated as Shape interface
    printShapeInfo(circle)      // Works with Circle
    printShapeInfo(rectangle)   // Works with Rectangle  
    printShapeInfo(triangle)    // Works with Triangle
    
    // Mix different shapes in same slice
    shapes := []Shape{circle, rectangle, triangle}
    fmt.Printf("Total area of all shapes: %.2f\n", totalArea(shapes))
    
    // Interface variables can hold any implementing type
    var shape Shape
    shape = circle
    fmt.Printf("Circle as interface: %.2f\n", shape.Area())
    
    shape = rectangle
    fmt.Printf("Rectangle as interface: %.2f\n", shape.Area())
}
```

### 2. **Interface Composition**
```go
// Small, focused interfaces
type Reader interface {
    Read([]byte) (int, error)
}

type Writer interface {
    Write([]byte) (int, error)
}

type Closer interface {
    Close() error
}

// Composed interfaces
type ReadWriter interface {
    Reader  // Embeds Reader interface
    Writer  // Embeds Writer interface
}

type ReadWriteCloser interface {
    Reader
    Writer
    Closer
}

// File-like struct that implements all interfaces
type File struct {
    name     string
    content  []byte
    position int
    closed   bool
}

func (f *File) Read(p []byte) (int, error) {
    if f.closed {
        return 0, fmt.Errorf("file is closed")
    }
    if f.position >= len(f.content) {
        return 0, fmt.Errorf("EOF")
    }
    
    n := copy(p, f.content[f.position:])
    f.position += n
    return n, nil
}

func (f *File) Write(p []byte) (int, error) {
    if f.closed {
        return 0, fmt.Errorf("file is closed")
    }
    
    f.content = append(f.content, p...)
    return len(p), nil
}

func (f *File) Close() error {
    if f.closed {
        return fmt.Errorf("file already closed")
    }
    f.closed = true
    fmt.Printf("File %s closed\n", f.name)
    return nil
}

// Functions using composed interfaces
func copyData(src Reader, dst Writer) error {
    buffer := make([]byte, 1024)
    for {
        n, err := src.Read(buffer)
        if err != nil {
            if err.Error() == "EOF" {
                break
            }
            return err
        }
        
        _, writeErr := dst.Write(buffer[:n])
        if writeErr != nil {
            return writeErr
        }
    }
    return nil
}

func processFile(rwc ReadWriteCloser) error {
    defer rwc.Close()  // Always close
    
    // Write some data
    _, err := rwc.Write([]byte("Hello, World!"))
    if err != nil {
        return err
    }
    
    // Reset position for reading (if supported)
    if f, ok := rwc.(*File); ok {
        f.position = 0
    }
    
    // Read it back
    buffer := make([]byte, 100)
    n, err := rwc.Read(buffer)
    if err != nil && err.Error() != "EOF" {
        return err
    }
    
    fmt.Printf("Read back: %s\n", string(buffer[:n]))
    return nil
}

func main() {
    // Create a file
    file := &File{name: "test.txt"}
    
    // Can be used as any of its interfaces
    var reader Reader = file
    var writer Writer = file
    var readWriter ReadWriter = file
    var readWriteCloser ReadWriteCloser = file
    
    // Write using Writer interface
    writer.Write([]byte("First line\n"))
    writer.Write([]byte("Second line\n"))
    
    // Reset position and read using Reader interface
    file.position = 0
    buffer := make([]byte, 50)
    n, _ := reader.Read(buffer)
    fmt.Printf("Read: %s", string(buffer[:n]))
    
    // Use composed interface
    file2 := &File{name: "test2.txt"}
    processFile(file2)
}
```

### 3. **Empty Interface and Type Assertions**
```go
// Empty interface can hold any type
func printAnything(value interface{}) {
    fmt.Printf("Value: %v, Type: %T\n", value, value)
}

// Type assertion - checking and extracting concrete type
func processValue(value interface{}) {
    // Type assertion with ok pattern (safe)
    if str, ok := value.(string); ok {
        fmt.Printf("String value: %s (length: %d)\n", str, len(str))
        return
    }
    
    if num, ok := value.(int); ok {
        fmt.Printf("Integer value: %d (double: %d)\n", num, num*2)
        return
    }
    
    if slice, ok := value.([]string); ok {
        fmt.Printf("String slice: %v (length: %d)\n", slice, len(slice))
        return
    }
    
    fmt.Printf("Unknown type: %T\n", value)
}

// Type switch - cleaner way to handle multiple types
func processValueSwitch(value interface{}) {
    switch v := value.(type) {
    case string:
        fmt.Printf("String: %s\n", v)
    case int:
        fmt.Printf("Integer: %d\n", v)
    case float64:
        fmt.Printf("Float: %.2f\n", v)
    case []string:
        fmt.Printf("String slice: %v\n", v)
    case bool:
        fmt.Printf("Boolean: %t\n", v)
    case nil:
        fmt.Println("Nil value")
    default:
        fmt.Printf("Unknown type: %T with value: %v\n", v, v)
    }
}

// Function that returns different types
func getDynamicValue(choice int) interface{} {
    switch choice {
    case 1:
        return "Hello, Go!"
    case 2:
        return 42
    case 3:
        return []string{"apple", "banana", "cherry"}
    case 4:
        return 3.14159
    case 5:
        return true
    default:
        return nil
    }
}

func main() {
    // Empty interface can hold anything
    var anything interface{}
    
    anything = "Hello"
    printAnything(anything)
    
    anything = 123
    printAnything(anything)
    
    anything = []int{1, 2, 3}
    printAnything(anything)
    
    // Process different types
    values := []interface{}{
        "Hello, World!",
        42,
        3.14159,
        []string{"Go", "is", "awesome"},
        true,
        nil,
        struct{ Name string }{Name: "Anonymous"},
    }
    
    fmt.Println("\nUsing type assertions:")
    for _, value := range values {
        processValue(value)
    }
    
    fmt.Println("\nUsing type switch:")
    for _, value := range values {
        processValueSwitch(value)
    }
    
    // Dynamic values
    fmt.Println("\nDynamic values:")
    for i := 1; i <= 6; i++ {
        value := getDynamicValue(i)
        processValueSwitch(value)
    }
}
```

## Common Interface Patterns

### 1. **Error Interface**
```go
// Built-in error interface
// type error interface {
//     Error() string
// }

// Custom error types
type ValidationError struct {
    Field   string
    Message string
}

func (ve ValidationError) Error() string {
    return fmt.Sprintf("validation error in field '%s': %s", ve.Field, ve.Message)
}

type NetworkError struct {
    Host    string
    Port    int
    Timeout bool
}

func (ne NetworkError) Error() string {
    if ne.Timeout {
        return fmt.Sprintf("timeout connecting to %s:%d", ne.Host, ne.Port)
    }
    return fmt.Sprintf("network error connecting to %s:%d", ne.Host, ne.Port)
}

// Functions that return different error types
func validateUser(name, email string) error {
    if name == "" {
        return ValidationError{
            Field:   "name",
            Message: "name cannot be empty",
        }
    }
    
    if !strings.Contains(email, "@") {
        return ValidationError{
            Field:   "email", 
            Message: "email must contain @",
        }
    }
    
    return nil
}

func connectToServer(host string, port int) error {
    if port <= 0 {
        return fmt.Errorf("invalid port: %d", port)
    }
    
    // Simulate network error
    if host == "unreachable.com" {
        return NetworkError{
            Host:    host,
            Port:    port,
            Timeout: true,
        }
    }
    
    return nil
}

// Error handling with type assertions
func handleError(err error) {
    if err == nil {
        return
    }
    
    // Check for specific error types
    switch e := err.(type) {
    case ValidationError:
        fmt.Printf("Validation failed: %s in field %s\n", e.Message, e.Field)
    case NetworkError:
        if e.Timeout {
            fmt.Printf("Connection timeout to %s:%d\n", e.Host, e.Port)
        } else {
            fmt.Printf("Network error to %s:%d\n", e.Host, e.Port)
        }
    default:
        fmt.Printf("General error: %s\n", err.Error())
    }
}

func main() {
    // Test validation errors
    err1 := validateUser("", "alice@example.com")
    handleError(err1)
    
    err2 := validateUser("Alice", "alice.example.com")
    handleError(err2)
    
    err3 := validateUser("Alice", "alice@example.com")
    handleError(err3)  // No error
    
    // Test network errors
    err4 := connectToServer("unreachable.com", 80)
    handleError(err4)
    
    err5 := connectToServer("example.com", -1)
    handleError(err5)
}
```

### 2. **Stringer Interface**
```go
// Built-in Stringer interface
// type Stringer interface {
//     String() string
// }

type Person struct {
    FirstName string
    LastName  string
    Age       int
    Email     string
}

// Implement Stringer interface
func (p Person) String() string {
    return fmt.Sprintf("%s %s (%d) - %s", p.FirstName, p.LastName, p.Age, p.Email)
}

type BankAccount struct {
    AccountNumber string
    Balance       float64
    Owner         string
}

func (ba BankAccount) String() string {
    return fmt.Sprintf("Account %s: %s has $%.2f", ba.AccountNumber, ba.Owner, ba.Balance)
}

// Color with custom string representation
type Color struct {
    Red, Green, Blue uint8
}

func (c Color) String() string {
    return fmt.Sprintf("rgb(%d, %d, %d)", c.Red, c.Green, c.Blue)
}

// Priority enum with string representation
type Priority int

const (
    Low Priority = iota
    Medium
    High
    Critical
)

func (p Priority) String() string {
    switch p {
    case Low:
        return "Low"
    case Medium:
        return "Medium"
    case High:
        return "High"
    case Critical:
        return "Critical"
    default:
        return "Unknown"
    }
}

type Task struct {
    ID          int
    Title       string
    Description string
    Priority    Priority
    Completed   bool
}

func (t Task) String() string {
    status := "Pending"
    if t.Completed {
        status = "Completed"
    }
    return fmt.Sprintf("Task #%d: %s [%s] (%s)", t.ID, t.Title, t.Priority, status)
}

func main() {
    // Person example
    person := Person{
        FirstName: "Alice",
        LastName:  "Johnson",
        Age:       30,
        Email:     "alice@example.com",
    }
    
    fmt.Println(person)  // Automatically calls person.String()
    fmt.Printf("Person: %s\n", person)  // Also calls String()
    fmt.Printf("Person details: %v\n", person)  // Also calls String()
    
    // Bank account example
    account := BankAccount{
        AccountNumber: "12345",
        Balance:       1500.50,
        Owner:         "Alice Johnson",
    }
    
    fmt.Println(account)
    
    // Color example
    red := Color{Red: 255, Green: 0, Blue: 0}
    blue := Color{Red: 0, Green: 0, Blue: 255}
    
    fmt.Printf("Primary colors: %s, %s\n", red, blue)
    
    // Task with Priority example
    tasks := []Task{
        {ID: 1, Title: "Fix bug", Priority: Critical, Completed: false},
        {ID: 2, Title: "Write tests", Priority: High, Completed: true},
        {ID: 3, Title: "Update docs", Priority: Low, Completed: false},
    }
    
    fmt.Println("Task list:")
    for _, task := range tasks {
        fmt.Printf("  %s\n", task)
    }
}
```

### 3. **Sort Interface**
```go
import "sort"

// Built-in sort.Interface
// type Interface interface {
//     Len() int
//     Less(i, j int) bool
//     Swap(i, j int)
// }

type Student struct {
    Name  string
    Grade int
    Age   int
}

// StudentsByGrade implements sort.Interface
type StudentsByGrade []Student

func (s StudentsByGrade) Len() int           { return len(s) }
func (s StudentsByGrade) Less(i, j int) bool { return s[i].Grade > s[j].Grade } // Higher grade first
func (s StudentsByGrade) Swap(i, j int)      { s[i], s[j] = s[j], s[i] }

// StudentsByName implements sort.Interface
type StudentsByName []Student

func (s StudentsByName) Len() int           { return len(s) }
func (s StudentsByName) Less(i, j int) bool { return s[i].Name < s[j].Name }
func (s StudentsByName) Swap(i, j int)      { s[i], s[j] = s[j], s[i] }

// Product for multiple sorting criteria
type Product struct {
    Name     string
    Price    float64
    Category string
    Rating   float64
}

type ProductsByPrice []Product

func (p ProductsByPrice) Len() int           { return len(p) }
func (p ProductsByPrice) Less(i, j int) bool { return p[i].Price < p[j].Price }
func (p ProductsByPrice) Swap(i, j int)      { p[i], p[j] = p[j], p[i] }

type ProductsByRating []Product

func (p ProductsByRating) Len() int           { return len(p) }
func (p ProductsByRating) Less(i, j int) bool { return p[i].Rating > p[j].Rating } // Higher rating first
func (p ProductsByRating) Swap(i, j int)      { p[i], p[j] = p[j], p[i] }

func main() {
    students := []Student{
        {"Alice", 95, 20},
        {"Bob", 87, 19},
        {"Charlie", 92, 21},
        {"Diana", 98, 20},
        {"Eve", 89, 18},
    }
    
    fmt.Println("Original students:")
    for _, student := range students {
        fmt.Printf("  %s: Grade %d, Age %d\n", student.Name, student.Grade, student.Age)
    }
    
    // Sort by grade (highest first)
    sort.Sort(StudentsByGrade(students))
    fmt.Println("\nSorted by grade (highest first):")
    for _, student := range students {
        fmt.Printf("  %s: Grade %d, Age %d\n", student.Name, student.Grade, student.Age)
    }
    
    // Sort by name (alphabetical)
    sort.Sort(StudentsByName(students))
    fmt.Println("\nSorted by name (alphabetical):")
    for _, student := range students {
        fmt.Printf("  %s: Grade %d, Age %d\n", student.Name, student.Grade, student.Age)
    }
    
    // Products example
    products := []Product{
        {"Laptop", 999.99, "Electronics", 4.5},
        {"Book", 19.99, "Education", 4.8},
        {"Phone", 699.99, "Electronics", 4.3},
        {"Desk", 299.99, "Furniture", 4.2},
        {"Headphones", 199.99, "Electronics", 4.7},
    }
    
    fmt.Println("\nOriginal products:")
    for _, product := range products {
        fmt.Printf("  %s: $%.2f, Rating: %.1f\n", product.Name, product.Price, product.Rating)
    }
    
    // Sort by price (lowest first)
    sort.Sort(ProductsByPrice(products))
    fmt.Println("\nSorted by price (lowest first):")
    for _, product := range products {
        fmt.Printf("  %s: $%.2f, Rating: %.1f\n", product.Name, product.Price, product.Rating)
    }
    
    // Sort by rating (highest first)
    sort.Sort(ProductsByRating(products))
    fmt.Println("\nSorted by rating (highest first):")
    for _, product := range products {
        fmt.Printf("  %s: $%.2f, Rating: %.1f\n", product.Name, product.Price, product.Rating)
    }
    
    // Using sort.Slice for ad-hoc sorting (Go 1.8+)
    sort.Slice(products, func(i, j int) bool {
        return products[i].Category < products[j].Category
    })
    fmt.Println("\nSorted by category (using sort.Slice):")
    for _, product := range products {
        fmt.Printf("  %s: %s, $%.2f\n", product.Name, product.Category, product.Price)
    }
}
```

## Advanced Interface Patterns

### 1. **Interface Segregation**
```go
// Bad: Large interface with many responsibilities
type BadFileProcessor interface {
    Read([]byte) (int, error)
    Write([]byte) (int, error)
    Close() error
    Compress() error
    Encrypt() error
    Backup() error
    Log(string)
    Validate() bool
}

// Good: Small, focused interfaces
type Reader interface {
    Read([]byte) (int, error)
}

type Writer interface {
    Write([]byte) (int, error)
}

type Closer interface {
    Close() error
}

type Compressor interface {
    Compress() error
}

type Encryptor interface {
    Encrypt() error
}

type Validator interface {
    Validate() bool
}

// Combine only what you need
type ReadWriter interface {
    Reader
    Writer
}

type SecureFile interface {
    ReadWriter
    Encryptor
    Validator
}

// Implementation
type SimpleFile struct {
    name    string
    content []byte
}

func (sf *SimpleFile) Read(p []byte) (int, error) {
    n := copy(p, sf.content)
    return n, nil
}

func (sf *SimpleFile) Write(p []byte) (int, error) {
    sf.content = append(sf.content, p...)
    return len(p), nil
}

func (sf *SimpleFile) Close() error {
    fmt.Printf("Closing file: %s\n", sf.name)
    return nil
}

type EncryptedFile struct {
    SimpleFile
    encrypted bool
}

func (ef *EncryptedFile) Encrypt() error {
    ef.encrypted = true
    fmt.Printf("Encrypting file: %s\n", ef.name)
    return nil
}

func (ef *EncryptedFile) Validate() bool {
    return ef.encrypted
}

// Functions using focused interfaces
func processReader(r Reader) {
    buffer := make([]byte, 100)
    n, _ := r.Read(buffer)
    fmt.Printf("Read %d bytes\n", n)
}

func processSecureFile(sf SecureFile) {
    sf.Write([]byte("sensitive data"))
    sf.Encrypt()
    if sf.Validate() {
        fmt.Println("File is secure")
    }
    processReader(sf)  // Can be used as Reader too
}

func main() {
    file := &EncryptedFile{
        SimpleFile: SimpleFile{name: "secret.txt"},
    }
    
    processSecureFile(file)
}
```

### 2. **Strategy Pattern with Interfaces**
```go
// Strategy interface
type PaymentProcessor interface {
    ProcessPayment(amount float64) error
    ValidatePayment(amount float64) bool
}

// Concrete strategies
type CreditCardProcessor struct {
    CardNumber string
    ExpiryDate string
}

func (ccp CreditCardProcessor) ProcessPayment(amount float64) error {
    if !ccp.ValidatePayment(amount) {
        return fmt.Errorf("credit card validation failed")
    }
    fmt.Printf("Processing credit card payment of $%.2f\n", amount)
    fmt.Printf("Card ending in %s\n", ccp.CardNumber[len(ccp.CardNumber)-4:])
    return nil
}

func (ccp CreditCardProcessor) ValidatePayment(amount float64) bool {
    return amount > 0 && amount <= 5000 && len(ccp.CardNumber) == 16
}

type PayPalProcessor struct {
    Email string
}

func (pp PayPalProcessor) ProcessPayment(amount float64) error {
    if !pp.ValidatePayment(amount) {
        return fmt.Errorf("PayPal validation failed")
    }
    fmt.Printf("Processing PayPal payment of $%.2f\n", amount)
    fmt.Printf("PayPal account: %s\n", pp.Email)
    return nil
}

func (pp PayPalProcessor) ValidatePayment(amount float64) bool {
    return amount > 0 && strings.Contains(pp.Email, "@")
}

type BankTransferProcessor struct {
    AccountNumber string
    RoutingNumber string
}

func (btp BankTransferProcessor) ProcessPayment(amount float64) error {
    if !btp.ValidatePayment(amount) {
        return fmt.Errorf("bank transfer validation failed")
    }
    fmt.Printf("Processing bank transfer of $%.2f\n", amount)
    fmt.Printf("To account: %s\n", btp.AccountNumber)
    return nil
}

func (btp BankTransferProcessor) ValidatePayment(amount float64) bool {
    return amount > 0 && len(btp.AccountNumber) >= 8 && len(btp.RoutingNumber) == 9
}

// Context that uses the strategy
type PaymentContext struct {
    processor PaymentProcessor
}

func (pc *PaymentContext) SetProcessor(processor PaymentProcessor) {
    pc.processor = processor
}

func (pc *PaymentContext) ProcessPayment(amount float64) error {
    if pc.processor == nil {
        return fmt.Errorf("no payment processor set")
    }
    return pc.processor.ProcessPayment(amount)
}

// Factory function
func GetPaymentProcessor(paymentType, identifier string) PaymentProcessor {
    switch paymentType {
    case "credit_card":
        return CreditCardProcessor{
            CardNumber: identifier,
            ExpiryDate: "12/25",
        }
    case "paypal":
        return PayPalProcessor{
            Email: identifier,
        }
    case "bank_transfer":
        return BankTransferProcessor{
            AccountNumber: identifier,
            RoutingNumber: "123456789",
        }
    default:
        return nil
    }
}

func main() {
    context := &PaymentContext{}
    
    // Test different payment strategies
    payments := []struct {
        paymentType string
        identifier  string
        amount      float64
    }{
        {"credit_card", "1234567890123456", 99.99},
        {"paypal", "alice@example.com", 149.50},
        {"bank_transfer", "87654321", 299.99},
    }
    
    for _, payment := range payments {
        fmt.Printf("\n--- Processing %s payment ---\n", payment.paymentType)
        
        processor := GetPaymentProcessor(payment.paymentType, payment.identifier)
        if processor == nil {
            fmt.Printf("Unknown payment type: %s\n", payment.paymentType)
            continue
        }
        
        context.SetProcessor(processor)
        err := context.ProcessPayment(payment.amount)
        if err != nil {
            fmt.Printf("Payment failed: %v\n", err)
        } else {
            fmt.Println("Payment successful!")
        }
    }
}
```

### 3. **Interface Adapters**
```go
// Target interface that our code expects
type ModernLogger interface {
    LogInfo(message string)
    LogError(message string)
    LogWarning(message string)
}

// Legacy logging system with different interface
type LegacyLogger struct {
    prefix string
}

func (ll *LegacyLogger) WriteLog(level, message string) {
    fmt.Printf("[%s] %s: %s\n", ll.prefix, level, message)
}

func (ll *LegacyLogger) WriteErrorLog(message string) {
    fmt.Printf("[%s] ERROR: %s\n", ll.prefix, message)
}

// Adapter that makes LegacyLogger work with ModernLogger interface
type LegacyLoggerAdapter struct {
    legacyLogger *LegacyLogger
}

func (lla *LegacyLoggerAdapter) LogInfo(message string) {
    lla.legacyLogger.WriteLog("INFO", message)
}

func (lla *LegacyLoggerAdapter) LogError(message string) {
    lla.legacyLogger.WriteErrorLog(message)
}

func (lla *LegacyLoggerAdapter) LogWarning(message string) {
    lla.legacyLogger.WriteLog("WARNING", message)
}

// Modern logger implementation
type StandardLogger struct {
    serviceName string
}

func (sl *StandardLogger) LogInfo(message string) {
    fmt.Printf("INFO [%s]: %s\n", sl.serviceName, message)
}

func (sl *StandardLogger) LogError(message string) {
    fmt.Printf("ERROR [%s]: %s\n", sl.serviceName, message)
}

func (sl *StandardLogger) LogWarning(message string) {
    fmt.Printf("WARNING [%s]: %s\n", sl.serviceName, message)
}

// Application code that uses ModernLogger interface
type Application struct {
    logger ModernLogger
}

func (app *Application) ProcessOrder(orderID string) {
    app.logger.LogInfo(fmt.Sprintf("Starting to process order %s", orderID))
    
    // Simulate some processing
    if orderID == "invalid" {
        app.logger.LogError("Invalid order ID")
        return
    }
    
    if orderID == "warning" {
        app.logger.LogWarning("Order processing took longer than expected")
    }
    
    app.logger.LogInfo(fmt.Sprintf("Successfully processed order %s", orderID))
}

func main() {
    // Use modern logger directly
    modernLogger := &StandardLogger{serviceName: "OrderService"}
    app1 := &Application{logger: modernLogger}
    
    fmt.Println("Using modern logger:")
    app1.ProcessOrder("12345")
    app1.ProcessOrder("warning")
    
    fmt.Println()
    
    // Use legacy logger through adapter
    legacyLogger := &LegacyLogger{prefix: "ORDER_SVC"}
    adapter := &LegacyLoggerAdapter{legacyLogger: legacyLogger}
    app2 := &Application{logger: adapter}
    
    fmt.Println("Using legacy logger through adapter:")
    app2.ProcessOrder("67890")
    app2.ProcessOrder("invalid")
}
```

## Interface Best Practices

### 1. **Accept Interfaces, Return Structs**
```go
// Good: Function accepts interface (flexible)
func ProcessData(r io.Reader) ([]byte, error) {
    data, err := ioutil.ReadAll(r)
    return data, err
}

// Good: Function returns concrete type (specific)
func NewFileReader(filename string) (*os.File, error) {
    return os.Open(filename)
}

// Less ideal: Function accepts concrete type (inflexible)
func ProcessDataFromFile(file *os.File) ([]byte, error) {
    data, err := ioutil.ReadAll(file)
    return data, err
}

// Less ideal: Function returns interface (ambiguous)
func NewReader(filename string) (io.Reader, error) {
    return os.Open(filename)  // What specific type is this?
}

func main() {
    // The good approach allows flexibility
    file, _ := os.Open("example.txt")
    data1, _ := ProcessData(file)  // Works with file
    
    stringReader := strings.NewReader("Hello, World!")
    data2, _ := ProcessData(stringReader)  // Also works with string reader
    
    fmt.Printf("Read %d bytes from file\n", len(data1))
    fmt.Printf("Read %d bytes from string: %s\n", len(data2), string(data2))
}
```

### 2. **Small, Focused Interfaces**
```go
// Good: Small, focused interfaces
type IDGenerator interface {
    GenerateID() string
}

type Timestamper interface {
    Timestamp() time.Time
}

type Validator interface {
    Validate() error
}

// Good: Compose when needed
type AuditableRecord interface {
    IDGenerator
    Timestamper
    Validator
}

// Implementation
type UserRecord struct {
    ID        string
    Name      string
    Email     string
    CreatedAt time.Time
}

func (ur *UserRecord) GenerateID() string {
    if ur.ID == "" {
        ur.ID = fmt.Sprintf("user_%d", time.Now().UnixNano())
    }
    return ur.ID
}

func (ur *UserRecord) Timestamp() time.Time {
    if ur.CreatedAt.IsZero() {
        ur.CreatedAt = time.Now()
    }
    return ur.CreatedAt
}

func (ur *UserRecord) Validate() error {
    if ur.Name == "" {
        return fmt.Errorf("name is required")
    }
    if !strings.Contains(ur.Email, "@") {
        return fmt.Errorf("invalid email")
    }
    return nil
}

// Functions can use just what they need
func generateRecordID(gen IDGenerator) string {
    return gen.GenerateID()
}

func validateRecord(v Validator) error {
    return v.Validate()
}

func auditRecord(ar AuditableRecord) error {
    // Use all aspects of the auditable record
    id := ar.GenerateID()
    timestamp := ar.Timestamp()
    err := ar.Validate()
    
    if err != nil {
        return err
    }
    
    fmt.Printf("Audit: Record %s created at %s\n", id, timestamp.Format(time.RFC3339))
    return nil
}

func main() {
    user := &UserRecord{
        Name:  "Alice Johnson",
        Email: "alice@example.com",
    }
    
    // Use specific interfaces
    id := generateRecordID(user)
    fmt.Printf("Generated ID: %s\n", id)
    
    err := validateRecord(user)
    if err != nil {
        fmt.Printf("Validation error: %v\n", err)
    } else {
        fmt.Println("Record is valid")
    }
    
    // Use composed interface
    err = auditRecord(user)
    if err != nil {
        fmt.Printf("Audit error: %v\n", err)
    }
}
```

### 3. **Interface Documentation and Testing**
```go
// DocumentStore interface defines behavior for document storage
type DocumentStore interface {
    // Store saves a document and returns its ID
    Store(doc Document) (string, error)
    
    // Retrieve gets a document by ID
    Retrieve(id string) (Document, error)
    
    // Delete removes a document by ID
    Delete(id string) error
    
    // List returns all document IDs
    List() ([]string, error)
}

type Document struct {
    ID      string
    Title   string
    Content string
}

// In-memory implementation for testing
type InMemoryDocumentStore struct {
    documents map[string]Document
    nextID    int
}

func NewInMemoryDocumentStore() *InMemoryDocumentStore {
    return &InMemoryDocumentStore{
        documents: make(map[string]Document),
        nextID:    1,
    }
}

func (store *InMemoryDocumentStore) Store(doc Document) (string, error) {
    if doc.Title == "" {
        return "", fmt.Errorf("document title cannot be empty")
    }
    
    if doc.ID == "" {
        doc.ID = fmt.Sprintf("doc_%d", store.nextID)
        store.nextID++
    }
    
    store.documents[doc.ID] = doc
    return doc.ID, nil
}

func (store *InMemoryDocumentStore) Retrieve(id string) (Document, error) {
    doc, exists := store.documents[id]
    if !exists {
        return Document{}, fmt.Errorf("document not found: %s", id)
    }
    return doc, nil
}

func (store *InMemoryDocumentStore) Delete(id string) error {
    if _, exists := store.documents[id]; !exists {
        return fmt.Errorf("document not found: %s", id)
    }
    delete(store.documents, id)
    return nil
}

func (store *InMemoryDocumentStore) List() ([]string, error) {
    ids := make([]string, 0, len(store.documents))
    for id := range store.documents {
        ids = append(ids, id)
    }
    return ids, nil
}

// Service that uses DocumentStore interface
type DocumentService struct {
    store DocumentStore
}

func NewDocumentService(store DocumentStore) *DocumentService {
    return &DocumentService{store: store}
}

func (service *DocumentService) CreateDocument(title, content string) (string, error) {
    doc := Document{
        Title:   title,
        Content: content,
    }
    return service.store.Store(doc)
}

func (service *DocumentService) GetDocument(id string) (Document, error) {
    return service.store.Retrieve(id)
}

func (service *DocumentService) ListAllDocuments() ([]Document, error) {
    ids, err := service.store.List()
    if err != nil {
        return nil, err
    }
    
    documents := make([]Document, 0, len(ids))
    for _, id := range ids {
        doc, err := service.store.Retrieve(id)
        if err != nil {
            return nil, err
        }
        documents = append(documents, doc)
    }
    
    return documents, nil
}

func main() {
    // Create service with in-memory store
    store := NewInMemoryDocumentStore()
    service := NewDocumentService(store)
    
    // Test the service
    fmt.Println("Testing document service:")
    
    // Create documents
    id1, _ := service.CreateDocument("Go Interfaces", "Understanding Go interfaces...")
    id2, _ := service.CreateDocument("Go Structs", "Working with Go structs...")
    
    fmt.Printf("Created documents: %s, %s\n", id1, id2)
    
    // Retrieve document
    doc, _ := service.GetDocument(id1)
    fmt.Printf("Retrieved: %s - %s\n", doc.Title, doc.Content[:20]+"...")
    
    // List all documents
    allDocs, _ := service.ListAllDocuments()
    fmt.Printf("Total documents: %d\n", len(allDocs))
    for _, doc := range allDocs {
        fmt.Printf("  - %s: %s\n", doc.ID, doc.Title)
    }
}
```

## How to Run This

```powershell
cd 17_interfaces
go run .
```

## Key Takeaways

1. **Interfaces define behavior, not data** - specify what something can do
2. **Implicit satisfaction** - no "implements" keyword needed
3. **Accept interfaces, return structs** - flexible parameters, specific returns
4. **Small, focused interfaces** - easier to implement and compose
5. **Empty interface for any type** - use type assertions and type switches carefully
6. **Interface composition** - embed smaller interfaces into larger ones
7. **Interfaces enable polymorphism** - same function works with different types
8. **Test with interface mocks** - implement interfaces for testing

Interfaces are like contracts that say "I don't care what you are, just that you can do these specific things." This makes your code incredibly flexible - you can swap out implementations without changing the code that uses them, making testing easier and your programs more modular!
