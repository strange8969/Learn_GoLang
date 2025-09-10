# Arrays - Fixed-Size Collections of Data

This folder teaches you about arrays - the most basic way to store multiple values of the same type in a fixed-size container. Think of arrays like numbered parking spaces that can only hold a specific number of cars.

## What This Teaches You

**Array Fundamentals:**
- Fixed-length collections where size is part of the type
- All elements must be the same type  
- Zero values and how arrays initialize
- Value semantics - copying arrays copies all elements
- When to use arrays vs slices

## The Code Explained Simply

```go
package main
import "fmt"

func main() {
    // Method 1: Declare array with zero values
    var nums [4]int           // Array of 4 integers, all start as 0
    fmt.Println(nums)         // Prints: [0 0 0 0]
    
    // Method 2: Access and modify individual elements
    nums[0] = 1               // Set first element (index 0)
    nums[1] = 2               // Set second element (index 1)
    fmt.Println(nums[0])      // Prints: 1
    fmt.Println(nums)         // Prints: [1 2 0 0]
    fmt.Println(len(nums))    // Prints: 4 (length is always fixed)
    
    // Method 3: Declare and initialize in one line
    vals := [3]int{1, 2, 3}   // Array of 3 integers with values
    fmt.Println(vals)         // Prints: [1 2 3]
    
    // Method 4: Different types work the same way
    var flags [4]bool         // Array of 4 booleans, all start as false
    flags[2] = true           // Set third element to true
    fmt.Println(flags)        // Prints: [false false true false]
    
    var names [3]string       // Array of 3 strings, all start as ""
    names[0] = "golang"       // Set first name
    fmt.Println(names)        // Prints: [golang   ] (empty strings show as spaces)
    
    // Method 5: 2D arrays (arrays of arrays)
    grid := [2][2]int{{3, 4}, {5, 6}}   // 2x2 grid
    fmt.Println(grid)         // Prints: [[3 4] [5 6]]
}
```

## Understanding Array Fundamentals

### 1. **Array Declaration Syntax**
```go
var arrayName [size]type

// Examples:
var ages [5]int           // 5 integers
var names [10]string      // 10 strings  
var scores [3]float64     // 3 floating point numbers
var flags [8]bool         // 8 boolean values
```

### 2. **Array Size is Part of the Type**
```go
var arr1 [3]int    // Type: [3]int
var arr2 [4]int    // Type: [4]int

// arr1 = arr2     // ❌ Compile error! Different types
// [3]int and [4]int are completely different types
```

### 3. **Zero Values by Default**
```go
var numbers [3]int     // [0, 0, 0]
var words [3]string    // ["", "", ""]  
var flags [3]bool      // [false, false, false]
var prices [3]float64  // [0.0, 0.0, 0.0]
```

### 4. **Indexing (Accessing Elements)**
```go
arr := [5]int{10, 20, 30, 40, 50}

fmt.Println(arr[0])    // First element: 10
fmt.Println(arr[4])    // Last element: 50
// fmt.Println(arr[5]) // ❌ Runtime panic! Index out of bounds

// Modifying elements
arr[1] = 99           // Change second element
fmt.Println(arr)      // [10, 99, 30, 40, 50]
```

## Different Ways to Create Arrays

### 1. **Zero-Initialized Arrays**
```go
var scores [5]int        // All elements are 0
var names [3]string      // All elements are ""
var active [4]bool       // All elements are false
```

### 2. **Arrays with Initial Values**
```go
// Specify all values
numbers := [4]int{10, 20, 30, 40}

// Specify some values (rest are zero)
partial := [5]int{1, 2}              // [1, 2, 0, 0, 0]

// Use specific indices
sparse := [5]int{0: 100, 2: 200}     // [100, 0, 200, 0, 0]
```

### 3. **Let Go Calculate the Size**
```go
// Use ... to let Go count the elements
auto := [...]int{1, 2, 3, 4, 5}     // Go makes this [5]int
fmt.Printf("Length: %d\n", len(auto)) // Length: 5
```

### 4. **Multi-dimensional Arrays**
```go
// 2D array (matrix)
matrix := [3][3]int{
    {1, 2, 3},
    {4, 5, 6}, 
    {7, 8, 9},
}

// Access 2D elements
fmt.Println(matrix[1][2])  // Row 1, Column 2: prints 6

// 3D array (rare but possible)
cube := [2][2][2]int{
    {{1, 2}, {3, 4}},
    {{5, 6}, {7, 8}},
}
```

## Value Semantics (Important!)

### Arrays are Copied When Assigned or Passed to Functions
```go
original := [3]int{1, 2, 3}
copy := original              // Copies ALL elements
copy[0] = 99                  // Change copy

fmt.Println(original)         // [1, 2, 3] - unchanged!
fmt.Println(copy)             // [99, 2, 3] - only copy changed

func modifyArray(arr [3]int) {
    arr[0] = 999              // Changes local copy only
}

numbers := [3]int{1, 2, 3}
modifyArray(numbers)          // Pass copy to function
fmt.Println(numbers)          // [1, 2, 3] - still unchanged!
```

### To Modify Original Array, Use Pointer
```go
func modifyArrayPointer(arr *[3]int) {
    arr[0] = 999              // Changes original through pointer
}

numbers := [3]int{1, 2, 3}
modifyArrayPointer(&numbers)  // Pass address of array
fmt.Println(numbers)          // [999, 2, 3] - now changed!
```

## Common Operations

### 1. **Iterating Through Arrays**
```go
fruits := [4]string{"apple", "banana", "cherry", "date"}

// Method 1: Traditional for loop
for i := 0; i < len(fruits); i++ {
    fmt.Printf("%d: %s\n", i, fruits[i])
}

// Method 2: Range loop (preferred)
for index, fruit := range fruits {
    fmt.Printf("%d: %s\n", index, fruit)
}

// Method 3: Just values (ignore index)
for _, fruit := range fruits {
    fmt.Println(fruit)
}

// Method 4: Just indices (ignore values)
for index := range fruits {
    fmt.Println("Index:", index)
}
```

### 2. **Finding Elements**
```go
numbers := [5]int{10, 20, 30, 20, 40}
target := 20
found := false
foundIndex := -1

for i, num := range numbers {
    if num == target {
        found = true
        foundIndex = i
        break  // Stop at first match
    }
}

if found {
    fmt.Printf("Found %d at index %d\n", target, foundIndex)
} else {
    fmt.Printf("%d not found\n", target)
}
```

### 3. **Calculating Statistics**
```go
scores := [5]float64{85.5, 92.0, 78.5, 90.0, 87.5}

// Sum and average
sum := 0.0
for _, score := range scores {
    sum += score
}
average := sum / float64(len(scores))

// Find max and min
max := scores[0]
min := scores[0]
for _, score := range scores {
    if score > max {
        max = score
    }
    if score < min {
        min = score
    }
}

fmt.Printf("Average: %.2f\n", average)
fmt.Printf("Max: %.2f, Min: %.2f\n", max, min)
```

## Real-World Examples

### 1. **Game Board (2D Array)**
```go
// Tic-tac-toe board
type Board [3][3]string

func createBoard() Board {
    return Board{
        {"-", "-", "-"},
        {"-", "-", "-"},
        {"-", "-", "-"},
    }
}

func printBoard(board Board) {
    for i := 0; i < 3; i++ {
        for j := 0; j < 3; j++ {
            fmt.Print(board[i][j], " ")
        }
        fmt.Println()
    }
}

board := createBoard()
board[1][1] = "X"  // Center square
board[0][0] = "O"  // Top-left corner
printBoard(board)
```

### 2. **Weekly Temperature Data**
```go
// Store temperatures for each day of the week
temperatures := [7]float64{72.5, 75.0, 68.3, 71.2, 73.8, 76.1, 74.5}
days := [7]string{"Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"}

// Find hottest day
maxTemp := temperatures[0]
maxDay := days[0]
for i, temp := range temperatures {
    if temp > maxTemp {
        maxTemp = temp
        maxDay = days[i]
    }
}

fmt.Printf("Hottest day: %s (%.1f°F)\n", maxDay, maxTemp)

// Calculate weekly average
sum := 0.0
for _, temp := range temperatures {
    sum += temp
}
average := sum / float64(len(temperatures))
fmt.Printf("Average temperature: %.1f°F\n", average)
```

### 3. **RGB Color Values**
```go
// Represent colors as arrays of RGB values
type Color [3]int  // [Red, Green, Blue]

// Predefined colors
red := Color{255, 0, 0}
green := Color{0, 255, 0}
blue := Color{0, 0, 255}
purple := Color{128, 0, 128}

// Color palette for an image
palette := [4]Color{red, green, blue, purple}

for i, color := range palette {
    fmt.Printf("Color %d: RGB(%d, %d, %d)\n", i, color[0], color[1], color[2])
}
```

## When to Use Arrays vs Slices

### Use Arrays When:
- **Fixed size known at compile time**: Days of week, months, game board
- **Performance critical**: Avoiding allocation overhead
- **Value semantics wanted**: Want copies, not shared references
- **Small, fixed collections**: Coordinates (x,y), RGB values, etc.

### Use Slices When:
- **Size can change**: User input, growing lists, dynamic data
- **Size not known until runtime**: Reading from files, databases
- **Need flexibility**: Most Go code uses slices
- **Working with APIs**: Most Go standard library uses slices

### Examples:
```go
// ✅ Good use of arrays
var weekdays [7]string = [7]string{"Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"}
var chessBoard [8][8]string
var rgbColor [3]int

// ✅ Better as slices  
var usernames []string        // Unknown how many users
var shoppingCart []Item       // Cart can grow/shrink
var searchResults []Result    // Results vary by query
```

## Benefits and Limitations

### Benefits:
- **Predictable memory usage** - size never changes
- **Cache-friendly** - elements stored contiguously in memory
- **Type safety** - can't accidentally change size
- **No allocation overhead** - memory allocated on stack

### Limitations:
- **Fixed size** - can't grow or shrink
- **Size part of type** - `[3]int` and `[4]int` are different types
- **Value copying** - passing large arrays copies all elements
- **Limited flexibility** - most Go code uses slices instead

## How to Run This

```powershell
cd 8_arrays
go run .
```

## Key Takeaways

1. **Arrays have fixed size** that's part of their type
2. **All elements are initialized** to zero values by default  
3. **Arrays are copied** when assigned or passed to functions
4. **Use slices for most cases** - arrays are for special situations
5. **Indexing starts at 0** and goes to length-1

Arrays are like numbered mailboxes in an apartment building - each box has a fixed address (index) and can hold one item of the same type, and the total number of boxes never changes!
