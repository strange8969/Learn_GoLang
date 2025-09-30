# Pointers - Understanding Memory Addresses in Go

This folder teaches you about pointers - one of Go's most powerful features for working directly with memory addresses. Think of pointers as "directions to where your data lives in memory" rather than copies of the data itself.

## What This Teaches You

**Pointer Fundamentals:**
- What pointers are and why they're useful
- Creating pointers with `&` (address operator)
- Dereferencing pointers with `*` (dereference operator) 
- Pointer types vs value types
- Nil pointers and pointer safety
- Pointer receivers for methods
- When to use pointers vs values

## The Code Explained Simply

```go
package main
import "fmt"

func main() {
    // Regular variable (value)
    x := 42
    fmt.Printf("x = %d\n", x)                    // 42
    fmt.Printf("Address of x: %p\n", &x)         // Memory address like 0xc0000140b8
    
    // Pointer variable (stores memory address)
    var p *int    // p is a pointer to an int
    p = &x        // p now points to x's memory address
    
    fmt.Printf("p = %p\n", p)                    // Same address as &x
    fmt.Printf("Value at address p: %d\n", *p)   // 42 (dereference p to get value)
    
    // Modify value through pointer
    *p = 100      // Changes x through the pointer
    fmt.Printf("x after modification: %d\n", x)  // 100
    
    // Create pointer and value in one line
    y := 200
    py := &y      // py is pointer to y
    fmt.Printf("y = %d, *py = %d\n", y, *py)    // Both are 200
}
```

## Pointer Basics

### 1. **Creating and Using Pointers**
```go
func main() {
    // Method 1: Create variable first, then get pointer
    name := "Alice"
    namePtr := &name
    
    fmt.Printf("Value: %s\n", name)              // Alice
    fmt.Printf("Address: %p\n", namePtr)         // 0xc0000... (memory address)
    fmt.Printf("Value via pointer: %s\n", *namePtr) // Alice
    
    // Method 2: Create pointer directly with new()
    agePtr := new(int)  // Creates int with zero value, returns pointer
    *agePtr = 25        // Set value through pointer
    
    fmt.Printf("Age: %d\n", *agePtr)             // 25
    fmt.Printf("Age address: %p\n", agePtr)      // 0xc0000...
    
    // Method 3: Create and initialize in one step
    score := 95
    scorePtr := &score
    
    // Modify original through pointer
    *scorePtr = 100
    fmt.Printf("Modified score: %d\n", score)    // 100
}
```

### 2. **Pointer Types**
```go
func main() {
    // Different pointer types
    var intPtr *int
    var stringPtr *string
    var boolPtr *bool
    var floatPtr *float64
    
    // Check if pointers are nil (uninitialized)
    fmt.Printf("intPtr is nil: %t\n", intPtr == nil)       // true
    fmt.Printf("stringPtr is nil: %t\n", stringPtr == nil) // true
    
    // Initialize pointers
    num := 42
    text := "Hello"
    flag := true
    pi := 3.14159
    
    intPtr = &num
    stringPtr = &text
    boolPtr = &flag
    floatPtr = &pi
    
    fmt.Printf("*intPtr = %d\n", *intPtr)         // 42
    fmt.Printf("*stringPtr = %s\n", *stringPtr)   // Hello
    fmt.Printf("*boolPtr = %t\n", *boolPtr)       // true
    fmt.Printf("*floatPtr = %.2f\n", *floatPtr)   // 3.14
    
    // Now pointers are not nil
    fmt.Printf("intPtr is nil: %t\n", intPtr == nil)  // false
}
```

### 3. **Nil Pointers and Safety**
```go
func main() {
    var p *int  // Uninitialized pointer is nil
    
    fmt.Printf("p is nil: %t\n", p == nil)  // true
    
    // ❌ This would cause a runtime panic!
    // fmt.Println(*p)  // panic: runtime error: invalid memory address
    
    // ✅ Always check for nil before dereferencing
    if p != nil {
        fmt.Printf("Value: %d\n", *p)
    } else {
        fmt.Println("Pointer is nil, cannot dereference")
    }
    
    // Initialize pointer safely
    num := 100
    p = &num
    
    // Now safe to dereference
    if p != nil {
        fmt.Printf("Value: %d\n", *p)  // 100
    }
    
    // Helper function to safely dereference
    fmt.Printf("Safe value: %d\n", safeIntValue(p))     // 100
    fmt.Printf("Safe value: %d\n", safeIntValue(nil))   // 0 (default)
}

// Helper function for safe pointer dereferencing
func safeIntValue(p *int) int {
    if p != nil {
        return *p
    }
    return 0  // Return zero value for nil pointer
}
```

## Pointers vs Values

### 1. **Function Parameters**
```go
// Pass by value - function gets a copy
func modifyValue(x int) {
    x = 999  // Only changes the local copy
    fmt.Printf("Inside modifyValue: x = %d\n", x)  // 999
}

// Pass by pointer - function can modify original
func modifyPointer(x *int) {
    *x = 999  // Changes the original value
    fmt.Printf("Inside modifyPointer: *x = %d\n", *x)  // 999
}

func main() {
    num := 42
    fmt.Printf("Original: %d\n", num)  // 42
    
    // Pass by value - original unchanged
    modifyValue(num)
    fmt.Printf("After modifyValue: %d\n", num)  // 42 (unchanged)
    
    // Pass by pointer - original changed
    modifyPointer(&num)
    fmt.Printf("After modifyPointer: %d\n", num)  // 999 (changed!)
}
```

### 2. **Memory and Performance**
```go
// Large struct for demonstration
type Person struct {
    Name    string
    Age     int
    Address string
    Phone   string
    Email   string
    // ... imagine many more fields
}

// Pass by value - copies entire struct (expensive for large structs)
func processPersonValue(p Person) {
    // This function receives a complete copy of the Person struct
    p.Age++  // Changes only affect the copy
    fmt.Printf("Processing %s (copy)\n", p.Name)
}

// Pass by pointer - only copies memory address (cheap!)
func processPersonPointer(p *Person) {
    // This function receives only the memory address (8 bytes on 64-bit)
    p.Age++  // Changes affect the original
    fmt.Printf("Processing %s (original)\n", p.Name)
}

func main() {
    person := Person{
        Name:    "Alice",
        Age:     30,
        Address: "123 Main St",
        Phone:   "555-1234",
        Email:   "alice@example.com",
    }
    
    fmt.Printf("Original age: %d\n", person.Age)  // 30
    
    // Pass by value - original unchanged, but slower for large structs
    processPersonValue(person)
    fmt.Printf("Age after value processing: %d\n", person.Age)  // 30 (unchanged)
    
    // Pass by pointer - original changed, faster for large structs
    processPersonPointer(&person)
    fmt.Printf("Age after pointer processing: %d\n", person.Age)  // 31 (changed!)
}
```

### 3. **Return Values**
```go
// Return by value - returns a copy
func createPersonValue() Person {
    return Person{Name: "Bob", Age: 25}  // Copies the struct
}

// Return by pointer - returns address of local variable (dangerous!)
func createPersonPointerBad() *Person {
    p := Person{Name: "Charlie", Age: 35}
    return &p  // ❌ Dangerous! Local variable address
}

// Return by pointer - safe way using new() or make()
func createPersonPointerGood() *Person {
    return &Person{Name: "Dave", Age: 45}  // Go handles memory allocation
}

// Factory function returning pointer
func newPerson(name string, age int) *Person {
    return &Person{
        Name: name,
        Age:  age,
    }
}

func main() {
    // Get person by value
    p1 := createPersonValue()
    fmt.Printf("Person by value: %+v\n", p1)
    
    // Get person by pointer (safe version)
    p2 := createPersonPointerGood()
    fmt.Printf("Person by pointer: %+v\n", *p2)
    
    // Factory function
    p3 := newPerson("Eve", 28)
    fmt.Printf("Factory person: %+v\n", *p3)
    
    // Modify through pointer
    p3.Age = 29
    fmt.Printf("Modified person: %+v\n", *p3)
}
```

## Pointer Methods (Receivers)

### 1. **Value vs Pointer Receivers**
```go
type Counter struct {
    count int
}

// Value receiver - method gets a copy of the struct
func (c Counter) GetCount() int {
    return c.count
}

// Value receiver - cannot modify original
func (c Counter) IncrementValue() {
    c.count++  // Only modifies the copy!
}

// Pointer receiver - method gets pointer to the struct
func (c *Counter) IncrementPointer() {
    c.count++  // Modifies the original!
}

// Pointer receiver - can modify original
func (c *Counter) Reset() {
    c.count = 0
}

// Pointer receiver - safe even if called on nil
func (c *Counter) SafeIncrement() {
    if c != nil {
        c.count++
    }
}

func main() {
    counter := Counter{count: 5}
    fmt.Printf("Initial count: %d\n", counter.GetCount())  // 5
    
    // Value receiver method - original unchanged
    counter.IncrementValue()
    fmt.Printf("After IncrementValue: %d\n", counter.GetCount())  // 5 (unchanged!)
    
    // Pointer receiver method - original changed
    counter.IncrementPointer()
    fmt.Printf("After IncrementPointer: %d\n", counter.GetCount())  // 6 (changed!)
    
    // Reset using pointer receiver
    counter.Reset()
    fmt.Printf("After Reset: %d\n", counter.GetCount())  // 0
    
    // Go allows calling pointer methods on values (automatic conversion)
    counter.IncrementPointer()  // Go converts to (&counter).IncrementPointer()
    fmt.Printf("Final count: %d\n", counter.GetCount())  // 1
    
    // Can also call on pointer explicitly
    ptr := &counter
    ptr.IncrementPointer()
    fmt.Printf("Via pointer: %d\n", counter.GetCount())  // 2
}
```

### 2. **When to Use Pointer Receivers**
```go
type BankAccount struct {
    accountNumber string
    balance       float64
    transactions  []string  // This could grow large
}

// Use pointer receiver when:
// 1. Method needs to modify the struct
func (ba *BankAccount) Deposit(amount float64) {
    if amount > 0 {
        ba.balance += amount
        ba.transactions = append(ba.transactions, 
            fmt.Sprintf("Deposited %.2f", amount))
    }
}

func (ba *BankAccount) Withdraw(amount float64) bool {
    if amount > 0 && ba.balance >= amount {
        ba.balance -= amount
        ba.transactions = append(ba.transactions, 
            fmt.Sprintf("Withdrew %.2f", amount))
        return true
    }
    return false
}

// 2. Struct is large (avoid copying)
func (ba *BankAccount) GetStatement() string {
    // Even read-only operations should use pointer receiver
    // if the struct is large to avoid copying
    var statement strings.Builder
    statement.WriteString(fmt.Sprintf("Account: %s\n", ba.accountNumber))
    statement.WriteString(fmt.Sprintf("Balance: %.2f\n", ba.balance))
    statement.WriteString("Transactions:\n")
    for _, transaction := range ba.transactions {
        statement.WriteString(fmt.Sprintf("  %s\n", transaction))
    }
    return statement.String()
}

// Use value receiver when:
// 3. Method only reads data and struct is small
func (ba BankAccount) GetBalance() float64 {
    return ba.balance  // Simple read operation, small struct
}

// 4. You want to work with copies (rare)
func (ba BankAccount) GetAccountCopy() BankAccount {
    return ba  // Returns a complete copy
}

func main() {
    account := &BankAccount{
        accountNumber: "12345",
        balance:       100.0,
        transactions:  []string{"Opening deposit"},
    }
    
    // Deposit and withdraw (modifies original)
    account.Deposit(50.0)
    account.Withdraw(25.0)
    
    fmt.Printf("Balance: %.2f\n", account.GetBalance())  // 125.00
    fmt.Println(account.GetStatement())
    
    // Can call pointer methods on value too
    account2 := BankAccount{accountNumber: "67890", balance: 200.0}
    account2.Deposit(100.0)  // Go automatically converts to (&account2).Deposit(100.0)
    fmt.Printf("Account2 balance: %.2f\n", account2.GetBalance())  // 300.00
}
```

## Advanced Pointer Patterns

### 1. **Pointer to Pointer**
```go
func main() {
    x := 42
    p := &x     // p is pointer to int
    pp := &p    // pp is pointer to pointer to int
    
    fmt.Printf("x = %d\n", x)           // 42
    fmt.Printf("*p = %d\n", *p)         // 42
    fmt.Printf("**pp = %d\n", **pp)     // 42 (double dereference)
    
    // Modify through pointer to pointer
    **pp = 100
    fmt.Printf("x after **pp = 100: %d\n", x)  // 100
    
    // Change what p points to through pp
    y := 200
    *pp = &y    // p now points to y instead of x
    fmt.Printf("*p after redirect: %d\n", *p)  // 200
    fmt.Printf("x is still: %d\n", x)          // 100
}

// Practical example: function that modifies a pointer
func makePointer(pp **int, value int) {
    temp := value
    *pp = &temp  // Make the pointer point to new value
}

// Better version using return
func createIntPointer(value int) *int {
    return &value  // Go handles the memory allocation
}
```

### 2. **Pointers in Data Structures**
```go
// Linked list node
type ListNode struct {
    Value int
    Next  *ListNode  // Pointer to next node
}

// Linked list
type LinkedList struct {
    Head *ListNode
    Tail *ListNode
    Size int
}

func (ll *LinkedList) Append(value int) {
    newNode := &ListNode{Value: value, Next: nil}
    
    if ll.Head == nil {
        // First node
        ll.Head = newNode
        ll.Tail = newNode
    } else {
        // Append to end
        ll.Tail.Next = newNode
        ll.Tail = newNode
    }
    ll.Size++
}

func (ll *LinkedList) Print() {
    current := ll.Head
    fmt.Print("List: ")
    for current != nil {
        fmt.Printf("%d ", current.Value)
        current = current.Next  // Move to next node
    }
    fmt.Println()
}

// Binary tree node
type TreeNode struct {
    Value int
    Left  *TreeNode
    Right *TreeNode
}

func (tn *TreeNode) Insert(value int) {
    if value < tn.Value {
        if tn.Left == nil {
            tn.Left = &TreeNode{Value: value}
        } else {
            tn.Left.Insert(value)
        }
    } else {
        if tn.Right == nil {
            tn.Right = &TreeNode{Value: value}
        } else {
            tn.Right.Insert(value)
        }
    }
}

func (tn *TreeNode) PrintInOrder() {
    if tn != nil {
        tn.Left.PrintInOrder()   // Print left subtree
        fmt.Printf("%d ", tn.Value)
        tn.Right.PrintInOrder()  // Print right subtree
    }
}

func main() {
    // Linked list example
    list := &LinkedList{}
    list.Append(1)
    list.Append(2)
    list.Append(3)
    list.Print()  // List: 1 2 3
    fmt.Printf("Size: %d\n", list.Size)
    
    // Binary tree example
    root := &TreeNode{Value: 5}
    root.Insert(3)
    root.Insert(7)
    root.Insert(1)
    root.Insert(9)
    
    fmt.Print("Tree (in-order): ")
    root.PrintInOrder()  // 1 3 5 7 9
    fmt.Println()
}
```

### 3. **Interface with Pointers**
```go
// Interface for drawable objects
type Drawable interface {
    Draw()
    Move(dx, dy int)
}

// Circle struct
type Circle struct {
    X, Y   int
    Radius int
}

// Pointer receiver methods
func (c *Circle) Draw() {
    fmt.Printf("Drawing circle at (%d,%d) with radius %d\n", c.X, c.Y, c.Radius)
}

func (c *Circle) Move(dx, dy int) {
    c.X += dx
    c.Y += dy
}

// Rectangle struct  
type Rectangle struct {
    X, Y, Width, Height int
}

func (r *Rectangle) Draw() {
    fmt.Printf("Drawing rectangle at (%d,%d) size %dx%d\n", r.X, r.Y, r.Width, r.Height)
}

func (r *Rectangle) Move(dx, dy int) {
    r.X += dx
    r.Y += dy
}

func drawAndMove(d Drawable) {
    d.Draw()
    d.Move(5, 5)
    fmt.Println("After moving:")
    d.Draw()
}

func main() {
    // Must use pointers since methods have pointer receivers
    circle := &Circle{X: 0, Y: 0, Radius: 10}
    rectangle := &Rectangle{X: 0, Y: 0, Width: 20, Height: 15}
    
    drawAndMove(circle)
    fmt.Println()
    drawAndMove(rectangle)
}
```

## Memory Management and Pointers

### 1. **Stack vs Heap**
```go
// Local variables typically go on stack
func stackExample() {
    x := 42      // On stack (typically)
    p := &x      // Pointer to stack variable
    fmt.Printf("Stack value: %d\n", *p)
    // x automatically cleaned up when function returns
}

// When Go moves variables to heap (escape analysis)
func heapExample() *int {
    x := 42      // Goes to heap because pointer is returned
    return &x    // Pointer escapes the function
}

func main() {
    stackExample()
    
    // This pointer refers to heap memory
    heapPtr := heapExample()
    fmt.Printf("Heap value: %d\n", *heapPtr)
    // Go's garbage collector will clean up heap memory when no longer referenced
}
```

### 2. **Pointer Slices and Memory Sharing**
```go
func main() {
    // Original data
    numbers := []int{1, 2, 3, 4, 5}
    
    // Create slice of pointers to elements
    var pointers []*int
    for i := range numbers {
        pointers = append(pointers, &numbers[i])
    }
    
    // Modify original through pointers
    *pointers[0] = 100
    *pointers[2] = 300
    
    fmt.Printf("Original slice: %v\n", numbers)  // [100, 2, 300, 4, 5]
    
    // Verify pointers point to same memory
    for i, ptr := range pointers {
        fmt.Printf("numbers[%d]=%d, *pointers[%d]=%d, same memory: %t\n", 
            i, numbers[i], i, *ptr, ptr == &numbers[i])
    }
}
```

### 3. **Avoiding Common Memory Issues**
```go
// ❌ Dangerous: returning pointer to local variable
func dangerousFunction() *int {
    x := 42
    return &x  // In some languages this would be dangerous
               // Go is safe - moves x to heap automatically
}

// ❌ Storing pointers to slice elements (can become invalid)
func dangerousSlicePointers() []*int {
    slice := []int{1, 2, 3}
    var pointers []*int
    
    for i := range slice {
        pointers = append(pointers, &slice[i])  // Dangerous if slice grows!
    }
    
    // If slice grows, underlying array might be reallocated
    // and pointers become invalid
    slice = append(slice, 4, 5, 6, 7, 8, 9, 10)  // Might reallocate
    
    return pointers  // These pointers might be invalid now!
}

// ✅ Safe: store indices instead of pointers
func safeSliceIndices() []int {
    return []int{0, 1, 2}  // Store indices, not pointers
}

// ✅ Safe: copy values if you need them
func safeSliceCopy() []int {
    slice := []int{1, 2, 3}
    return append([]int(nil), slice...)  // Return copy
}

func main() {
    // This is actually safe in Go due to escape analysis
    ptr := dangerousFunction()
    fmt.Printf("Value: %d\n", *ptr)  // 42 - works fine
    
    // Demonstrate why slice pointers can be problematic
    // (Though Go's garbage collector might save us here too)
    indices := safeSliceIndices()
    fmt.Printf("Safe indices: %v\n", indices)
}
```

## Practical Pointer Examples

### 1. **Configuration Pattern**
```go
type Config struct {
    Host     string
    Port     int
    Database string
    Timeout  time.Duration
}

// Option function type
type ConfigOption func(*Config)

// Option functions
func WithHost(host string) ConfigOption {
    return func(c *Config) {
        c.Host = host
    }
}

func WithPort(port int) ConfigOption {
    return func(c *Config) {
        c.Port = port
    }
}

func WithDatabase(db string) ConfigOption {
    return func(c *Config) {
        c.Database = db
    }
}

func WithTimeout(timeout time.Duration) ConfigOption {
    return func(c *Config) {
        c.Timeout = timeout
    }
}

// Constructor with options
func NewConfig(options ...ConfigOption) *Config {
    // Default configuration
    config := &Config{
        Host:     "localhost",
        Port:     8080,
        Database: "default",
        Timeout:  30 * time.Second,
    }
    
    // Apply options
    for _, option := range options {
        option(config)
    }
    
    return config
}

func main() {
    // Create config with custom options
    config := NewConfig(
        WithHost("production.server.com"),
        WithPort(443),
        WithDatabase("production_db"),
        WithTimeout(60*time.Second),
    )
    
    fmt.Printf("Config: %+v\n", *config)
}
```

### 2. **Cache with Pointers**
```go
type Cache struct {
    data map[string]*CacheItem
    mu   sync.RWMutex  // Protect concurrent access
}

type CacheItem struct {
    Value     interface{}
    ExpiresAt time.Time
}

func NewCache() *Cache {
    return &Cache{
        data: make(map[string]*CacheItem),
    }
}

func (c *Cache) Set(key string, value interface{}, ttl time.Duration) {
    c.mu.Lock()
    defer c.mu.Unlock()
    
    c.data[key] = &CacheItem{
        Value:     value,
        ExpiresAt: time.Now().Add(ttl),
    }
}

func (c *Cache) Get(key string) (interface{}, bool) {
    c.mu.RLock()
    defer c.mu.RUnlock()
    
    item, exists := c.data[key]
    if !exists {
        return nil, false
    }
    
    if time.Now().After(item.ExpiresAt) {
        delete(c.data, key)  // Clean up expired item
        return nil, false
    }
    
    return item.Value, true
}

func (c *Cache) Delete(key string) {
    c.mu.Lock()
    defer c.mu.Unlock()
    delete(c.data, key)
}

func main() {
    cache := NewCache()
    
    // Store some values
    cache.Set("user:123", "Alice", 5*time.Second)
    cache.Set("session:abc", "active", 10*time.Second)
    
    // Retrieve values
    if value, found := cache.Get("user:123"); found {
        fmt.Printf("User: %s\n", value)
    }
    
    // Wait and check expiration
    time.Sleep(6 * time.Second)
    if _, found := cache.Get("user:123"); !found {
        fmt.Println("User cache expired")
    }
}
```

## Performance Considerations

### 1. **When to Use Pointers vs Values**
```go
type SmallStruct struct {
    ID   int
    Name string
}

type LargeStruct struct {
    Data [1000]int
    Name string
    // ... many more fields
}

// For small structs, values are often fine
func processSmallValue(s SmallStruct) {
    // Fast: small copy, good cache locality
}

func processSmallPointer(s *SmallStruct) {
    // Also fine: saves a small copy
}

// For large structs, always use pointers
func processLargeValue(s LargeStruct) {
    // Slow: copies 1000 ints + string every call!
}

func processLargePointer(s *LargeStruct) {
    // Fast: only copies pointer (8 bytes on 64-bit)
}

func benchmarkExample() {
    small := SmallStruct{ID: 1, Name: "test"}
    large := LargeStruct{Name: "test"}
    
    // Both are reasonable for small structs
    processSmallValue(small)
    processSmallPointer(&small)
    
    // Only pointer version is reasonable for large structs
    // processLargeValue(large)     // Don't do this!
    processLargePointer(&large)     // Do this instead
}
```

### 2. **Pointer vs Interface Performance**
```go
type Processor interface {
    Process() string
}

type DataProcessor struct {
    data []byte
}

func (dp *DataProcessor) Process() string {
    return fmt.Sprintf("Processed %d bytes", len(dp.data))
}

// Direct pointer call - fastest
func usePointer(dp *DataProcessor) string {
    return dp.Process()
}

// Interface call - slightly slower (virtual dispatch)
func useInterface(p Processor) string {
    return p.Process()
}

func main() {
    processor := &DataProcessor{data: make([]byte, 1000)}
    
    // Direct call
    result1 := usePointer(processor)
    fmt.Println(result1)
    
    // Interface call
    result2 := useInterface(processor)
    fmt.Println(result2)
    
    // For hot paths where performance matters, direct calls are faster
    // For flexibility and testing, interfaces are better
}
```

## Common Pitfalls

### 1. **Nil Pointer Dereference**
```go
func main() {
    var p *int
    
    // ❌ This will panic!
    // fmt.Println(*p)  // panic: runtime error: invalid memory address
    
    // ✅ Always check for nil
    if p != nil {
        fmt.Println(*p)
    } else {
        fmt.Println("Pointer is nil")
    }
    
    // ✅ Or use safe helper functions
    fmt.Println("Safe value:", safeIntValue(p))  // Returns 0 for nil
}

// Safe dereference with default
func safeIntValue(p *int) int {
    if p != nil {
        return *p
    }
    return 0
}

// Safe dereference with custom default
func intValueOrDefault(p *int, defaultValue int) int {
    if p != nil {
        return *p
    }
    return defaultValue
}
```

### 2. **Pointer to Loop Variable**
```go
func main() {
    numbers := []int{1, 2, 3, 4, 5}
    var pointers []*int
    
    // ❌ Wrong: all pointers point to the same variable
    for _, num := range numbers {
        pointers = append(pointers, &num)  // All point to same 'num' variable!
    }
    
    fmt.Println("Wrong way:")
    for i, ptr := range pointers {
        fmt.Printf("pointers[%d] = %d\n", i, *ptr)  // All print 5!
    }
    
    // ✅ Right: create new variable or use index
    pointers = nil  // Clear slice
    for _, num := range numbers {
        num := num  // Create new variable
        pointers = append(pointers, &num)
    }
    
    fmt.Println("\nRight way:")
    for i, ptr := range pointers {
        fmt.Printf("pointers[%d] = %d\n", i, *ptr)  // Prints 1, 2, 3, 4, 5
    }
    
    // ✅ Or use indices
    pointers = nil
    for i := range numbers {
        pointers = append(pointers, &numbers[i])  // Point to slice elements
    }
    
    fmt.Println("\nUsing indices:")
    for i, ptr := range pointers {
        fmt.Printf("pointers[%d] = %d\n", i, *ptr)  // Prints 1, 2, 3, 4, 5
    }
}
```

### 3. **Modifying Slice During Iteration**
```go
func main() {
    numbers := []*int{}
    
    // Create some numbers
    for i := 1; i <= 5; i++ {
        i := i  // Capture loop variable
        numbers = append(numbers, &i)
    }
    
    fmt.Println("Before modification:")
    for i, ptr := range numbers {
        fmt.Printf("numbers[%d] = %d\n", i, *ptr)
    }
    
    // ❌ Dangerous: modifying slice during iteration with pointers
    // This can cause issues if slice reallocates
    
    // ✅ Safe: iterate by index when modifying
    for i := 0; i < len(numbers); i++ {
        if *numbers[i] == 3 {
            // Remove element at index i
            numbers = append(numbers[:i], numbers[i+1:]...)
            i--  // Adjust index
        }
    }
    
    fmt.Println("\nAfter removing 3:")
    for i, ptr := range numbers {
        fmt.Printf("numbers[%d] = %d\n", i, *ptr)
    }
}
```

## How to Run This

```powershell
cd 15_pointers
go run .
```

## Key Takeaways

1. **Pointers store memory addresses** - use `&` to get address, `*` to get value
2. **Always check for nil** - dereferencing nil pointer causes panic
3. **Use pointer receivers for methods** - when you need to modify the struct or struct is large
4. **Pass by pointer vs value** - pointers for modification and large structs, values for small read-only data
5. **Go handles memory automatically** - garbage collector cleans up unreferenced memory
6. **Pointers enable efficient data sharing** - multiple variables can point to same data
7. **Be careful with loop variables** - capture them properly when taking addresses

Pointers are like having GPS coordinates to your data - instead of carrying copies of everything around, you just carry directions to where the real data lives. This makes your programs more efficient and enables powerful patterns like shared state and method receivers!
