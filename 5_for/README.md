# For Loops - Repeating Code Efficiently

This folder teaches you Go's only looping construct - the `for` loop. Unlike other languages that have `while`, `do-while`, and `foreach`, Go uses just `for` for all repetitive tasks.

## What This Teaches You

**Three Styles of For Loops:**
- **Classic for loop**: Traditional `init; condition; increment` style
- **While-style loop**: Just a condition, like a while loop in other languages  
- **Infinite loop**: Runs forever until you break out of it
- **Range-based loop**: For iterating over collections (covered in range section)

## The Code Explained Simply

```go
package main

func main() {
    // Style 1: While-style loop (like while in other languages)
    i := 1
    for i <= 3 {        // Keep looping while i is less than or equal to 3
        fmt.Println(i)   // Print current value
        i = i + 1        // Increment i (or loop runs forever!)
    }
    // Prints: 1, 2, 3
    
    // Style 2: Infinite loop (be careful!)
    for {
        println("This runs forever")
        break  // Exit the loop (without this, program never stops!)
    }
    
    // Style 3: Classic for loop (most common)
    for i := 0; i <= 3; i++ {  // Start at 0, while i <= 3, increment after each iteration
        if i == 2 {
            continue        // Skip the rest of this iteration, go to next
        }
        fmt.Println(i)      // Print current value
    }
    // Prints: 0, 1, 3 (skips 2 due to continue)
    
    // Style 4: Range over numbers (Go 1.22+)
    for i := range 11 {     // Loop from 0 to 10
        fmt.Println(i)      // Print each number
    }
    // Prints: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
}
```

## Understanding Each Loop Style

### 1. While-Style Loop
**Best for**: When you don't know exactly how many times to loop

```go
attempts := 0
for attempts < 5 {           // Keep trying up to 5 times
    success := tryLogin()     // Attempt login
    if success {
        break                // Exit loop if successful
    }
    attempts++               // Increment counter
}
```

**Real-world example**: Reading user input until they enter valid data
```go
var userAge int
for userAge < 1 || userAge > 120 {  // Keep asking until valid age
    fmt.Print("Enter your age (1-120): ")
    fmt.Scanf("%d", &userAge)
}
```

### 2. Classic For Loop
**Best for**: When you know exactly how many iterations you need

```go
// Print numbers 1 to 10
for i := 1; i <= 10; i++ {
    fmt.Println("Number:", i)
}

// Countdown timer
for seconds := 10; seconds > 0; seconds-- {
    fmt.Println("Time remaining:", seconds)
    time.Sleep(1 * time.Second)
}
fmt.Println("Time's up!")
```

**Structure breakdown**:
```go
for initialization; condition; increment {
    // code to repeat
}
```
- **Initialization**: `i := 0` - Run once at the start
- **Condition**: `i < 5` - Check before each iteration
- **Increment**: `i++` - Run after each iteration

### 3. Infinite Loop
**Best for**: Server programs, continuous monitoring, game loops

```go
// Web server that runs forever
for {
    request := waitForRequest()   // Wait for incoming request
    handleRequest(request)        // Process the request
    // Loop continues indefinitely
}

// Game loop
for {
    processInput()               // Handle player input
    updateGame()                 // Update game state  
    renderGraphics()             // Draw the screen
    
    if gameOver {
        break                    // Exit when game ends
    }
}
```

## Loop Control: Break and Continue

### Break - Exit the Loop Completely
```go
for i := 1; i <= 10; i++ {
    if i == 5 {
        break           // Stop looping when i equals 5
    }
    fmt.Println(i)      // Prints: 1, 2, 3, 4
}
fmt.Println("Done")     // This runs after break
```

### Continue - Skip to Next Iteration
```go
for i := 1; i <= 5; i++ {
    if i == 3 {
        continue        // Skip the rest when i equals 3
    }
    fmt.Println(i)      // Prints: 1, 2, 4, 5 (skips 3)
}
```

## Common Patterns and Examples

### Counting and Accumulating
```go
// Sum numbers from 1 to 100
total := 0
for i := 1; i <= 100; i++ {
    total += i
}
fmt.Println("Sum:", total)  // Prints: Sum: 5050

// Count even numbers
evenCount := 0
for i := 1; i <= 20; i++ {
    if i%2 == 0 {           // If number is even
        evenCount++
    }
}
fmt.Println("Even numbers:", evenCount)  // Prints: Even numbers: 10
```

### Processing Collections
```go
// Print each character in a string
word := "golang"
for i := 0; i < len(word); i++ {
    fmt.Printf("Character %d: %s\n", i, string(word[i]))
}
```

### Nested Loops (Loops Inside Loops)
```go
// Multiplication table
for i := 1; i <= 3; i++ {
    for j := 1; j <= 3; j++ {
        fmt.Printf("%d x %d = %d\t", i, j, i*j)
    }
    fmt.Println()  // New line after each row
}
// Output:
// 1 x 1 = 1    1 x 2 = 2    1 x 3 = 3
// 2 x 1 = 2    2 x 2 = 4    2 x 3 = 6  
// 3 x 1 = 3    3 x 2 = 6    3 x 3 = 9
```

## Key Concepts to Remember

1. **Go only has `for` loops** - no `while` or `do-while` keywords
2. **Always update your counter** in while-style loops or you'll get an infinite loop
3. **Use `break`** to exit a loop early
4. **Use `continue`** to skip to the next iteration
5. **Infinite loops are useful** for servers and continuous processes

## Common Beginner Mistakes

### 1. Forgetting to Increment in While-Style Loop
```go
// ❌ This runs forever!
i := 1
for i <= 5 {
    fmt.Println(i)
    // Forgot: i++
}

// ✅ Correct version
i := 1
for i <= 5 {
    fmt.Println(i)
    i++  // Don't forget this!
}
```

### 2. Off-by-One Errors
```go
// ❌ Prints 0,1,2,3,4 (5 numbers)
for i := 0; i < 5; i++ {
    fmt.Println(i)
}

// ✅ If you want exactly 5 iterations starting from 1
for i := 1; i <= 5; i++ {
    fmt.Println(i)  // Prints 1,2,3,4,5
}
```

### 3. Infinite Loop Without Break
```go
// ❌ This will never stop!
for {
    fmt.Println("Hello")
    // Need a break condition!
}

// ✅ Correct version with exit condition
counter := 0
for {
    fmt.Println("Hello")
    counter++
    if counter >= 3 {
        break
    }
}
```

## How to Run This

```powershell
cd 5_for
go run .
```

## Real-World Applications

- **File Processing**: Loop through lines in a file
- **User Input**: Keep asking until valid input received  
- **Game Development**: Main game loop, animation frames
- **Web Servers**: Process requests continuously
- **Data Processing**: Iterate through database records
- **Algorithms**: Sorting, searching, mathematical calculations

For loops are like the engine of repetition in your programs - they let you do the same task multiple times efficiently without writing the same code over and over!
