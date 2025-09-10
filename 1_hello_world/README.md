# Hello World - Your First Go Program

This is where you learn the absolute basics of creating and running a Go program.

## What This Teaches You

**The Foundation of Every Go Program:**
- Every Go program starts with `package main` - this tells Go this is an executable program
- The `main()` function is where your program begins running - it's like the starting point
- You need to import packages to use their features - `fmt` is for formatting and printing
- `fmt.Println()` prints text to the screen and adds a new line

## The Code Explained Simply

```go
package main        // This file belongs to the "main" package (executable)
import "fmt"        // Import the "fmt" package for printing functions
func main() {       // The main function - where execution starts
    fmt.Println("hello world")  // Print "hello world" to the screen
}
```

## Key Concepts to Remember

1. **Package Declaration**: Always starts with `package` - tells Go what this file is
2. **Imports**: Bring in code from other packages you want to use
3. **Main Function**: Every executable Go program must have exactly one `main()` function
4. **Printing**: `fmt.Println()` is the most common way to display output

## Common Beginner Mistakes

- Forgetting `package main` at the top
- Not importing `fmt` when you want to print
- Spelling `main` wrong in the function name
- Using single quotes instead of double quotes for strings

## How to Run This

```powershell
# Navigate to the folder
cd 1_hello_world

# Run the program
go run main.go

# Or just run everything in the folder
go run .
```

## What You Should See

When you run this program, you'll see:
```
hello world
```

That's it! You've successfully run your first Go program. This simple program demonstrates the fundamental structure that every Go program follows.
