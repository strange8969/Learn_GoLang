# Generics - Type Parameters and Generic Programming

This folder teaches you about generics in Go (introduced in Go 1.18) - a powerful feature that lets you write code that works with different types while maintaining type safety. Think of generics as templates that let you create functions, types, and methods that can work with any type you specify, eliminating code duplication and improving type safety.

## What This Teaches You

**Generics Fundamentals:**
- Type parameters and type constraints
- Generic functions and methods
- Generic types (structs, interfaces, etc.)
- Type inference and explicit type specification
- Common constraint patterns
- Interface constraints and type sets
- Performance implications of generics

## The Code Explained Simply

```go
package main
import "fmt"

// Generic function with type parameter T
func Max[T comparable](a, b T) T {
    if a > b {
        return a
    }
    return b
}

// Generic slice function
func Contains[T comparable](slice []T, item T) bool {
    for _, v := range slice {
        if v == item {
            return true
        }
    }
    return false
}

// Generic struct
type Pair[T, U any] struct {
    First  T
    Second U
}

func (p Pair[T, U]) String() string {
    return fmt.Sprintf("(%v, %v)", p.First, p.Second)
}

func main() {
    // Generic function with different types
    fmt.Printf("Max(5, 3) = %d\n", Max(5, 3))           // int
    fmt.Printf("Max(5.5, 3.2) = %.1f\n", Max(5.5, 3.2)) // float64
    fmt.Printf("Max('b', 'a') = %c\n", Max('b', 'a'))    // rune
    
    // Generic slice operations
    numbers := []int{1, 2, 3, 4, 5}
    fmt.Printf("Contains 3: %t\n", Contains(numbers, 3))      // true
    
    words := []string{"apple", "banana", "cherry"}
    fmt.Printf("Contains 'banana': %t\n", Contains(words, "banana")) // true
    
    // Generic struct with different type combinations
    intStringPair := Pair[int, string]{First: 42, Second: "answer"}
    floatBoolPair := Pair[float64, bool]{First: 3.14, Second: true}
    
    fmt.Printf("Int-String pair: %s\n", intStringPair)  // (42, answer)
    fmt.Printf("Float-Bool pair: %s\n", floatBoolPair)  // (3.14, true)
}
```

## Basic Generic Functions

### 1. **Simple Generic Functions**
```go
// Generic function to find minimum
func Min[T constraints.Ordered](a, b T) T {
    if a < b {
        return a
    }
    return b
}

// Generic function to swap two values
func Swap[T any](a, b T) (T, T) {
    return b, a
}

// Generic function to get first element of slice
func First[T any](slice []T) (T, bool) {
    var zero T
    if len(slice) == 0 {
        return zero, false
    }
    return slice[0], true
}

// Generic function to get last element of slice
func Last[T any](slice []T) (T, bool) {
    var zero T
    if len(slice) == 0 {
        return zero, false
    }
    return slice[len(slice)-1], true
}

// Generic function to reverse a slice
func Reverse[T any](slice []T) []T {
    result := make([]T, len(slice))
    for i, v := range slice {
        result[len(slice)-1-i] = v
    }
    return result
}

// Generic function with multiple type parameters
func Transform[T, U any](slice []T, fn func(T) U) []U {
    result := make([]U, len(slice))
    for i, v := range slice {
        result[i] = fn(v)
    }
    return result
}

func main() {
    // Test Min function
    fmt.Printf("Min(10, 5) = %d\n", Min(10, 5))
    fmt.Printf("Min(3.14, 2.71) = %.2f\n", Min(3.14, 2.71))
    fmt.Printf("Min('z', 'a') = %c\n", Min('z', 'a'))
    
    // Test Swap function
    a, b := 1, 2
    fmt.Printf("Before swap: a=%d, b=%d\n", a, b)
    a, b = Swap(a, b)
    fmt.Printf("After swap: a=%d, b=%d\n", a, b)
    
    // Test First and Last
    numbers := []int{10, 20, 30, 40, 50}
    if first, ok := First(numbers); ok {
        fmt.Printf("First element: %d\n", first)
    }
    
    if last, ok := Last(numbers); ok {
        fmt.Printf("Last element: %d\n", last)
    }
    
    // Test with empty slice
    var empty []string
    if _, ok := First(empty); !ok {
        fmt.Println("Empty slice has no first element")
    }
    
    // Test Reverse
    words := []string{"apple", "banana", "cherry"}
    reversed := Reverse(words)
    fmt.Printf("Original: %v\n", words)
    fmt.Printf("Reversed: %v\n", reversed)
    
    // Test Transform
    lengths := Transform(words, func(s string) int {
        return len(s)
    })
    fmt.Printf("Word lengths: %v\n", lengths)
    
    // Transform numbers to strings
    numberStrings := Transform(numbers, func(n int) string {
        return fmt.Sprintf("num_%d", n)
    })
    fmt.Printf("Number strings: %v\n", numberStrings)
}
```

### 2. **Generic Functions with Constraints**
```go
import "golang.org/x/exp/constraints"

// Numeric constraint for mathematical operations
type Numeric interface {
    constraints.Integer | constraints.Float
}

// Generic function to calculate sum
func Sum[T Numeric](numbers []T) T {
    var sum T
    for _, num := range numbers {
        sum += num
    }
    return sum
}

// Generic function to calculate average
func Average[T constraints.Float](numbers []T) T {
    if len(numbers) == 0 {
        return 0
    }
    return Sum(numbers) / T(len(numbers))
}

// Generic function for absolute value
func Abs[T Numeric](x T) T {
    if x < 0 {
        return -x
    }
    return x
}

// Generic function to find maximum in slice
func MaxSlice[T constraints.Ordered](slice []T) (T, bool) {
    var zero T
    if len(slice) == 0 {
        return zero, false
    }
    
    max := slice[0]
    for _, v := range slice[1:] {
        if v > max {
            max = v
        }
    }
    return max, true
}

// Generic function to filter slice
func Filter[T any](slice []T, predicate func(T) bool) []T {
    var result []T
    for _, v := range slice {
        if predicate(v) {
            result = append(result, v)
        }
    }
    return result
}

// Generic function to map and filter
func MapFilter[T, U any](slice []T, mapper func(T) (U, bool)) []U {
    var result []U
    for _, v := range slice {
        if mapped, ok := mapper(v); ok {
            result = append(result, mapped)
        }
    }
    return result
}

func main() {
    // Test Sum with different numeric types
    intNumbers := []int{1, 2, 3, 4, 5}
    fmt.Printf("Sum of integers: %d\n", Sum(intNumbers))  // 15
    
    floatNumbers := []float64{1.1, 2.2, 3.3}
    fmt.Printf("Sum of floats: %.1f\n", Sum(floatNumbers))  // 6.6
    
    // Test Average
    fmt.Printf("Average of floats: %.2f\n", Average(floatNumbers))  // 2.20
    
    // Test Abs
    fmt.Printf("Abs(-5) = %d\n", Abs(-5))
    fmt.Printf("Abs(-3.14) = %.2f\n", Abs(-3.14))
    
    // Test MaxSlice
    if max, ok := MaxSlice(intNumbers); ok {
        fmt.Printf("Maximum: %d\n", max)  // 5
    }
    
    stringSlice := []string{"apple", "zebra", "banana"}
    if max, ok := MaxSlice(stringSlice); ok {
        fmt.Printf("Maximum string: %s\n", max)  // zebra
    }
    
    // Test Filter
    evenNumbers := Filter(intNumbers, func(n int) bool {
        return n%2 == 0
    })
    fmt.Printf("Even numbers: %v\n", evenNumbers)  // [2, 4]
    
    longWords := Filter([]string{"go", "programming", "is", "awesome"}, func(s string) bool {
        return len(s) > 3
    })
    fmt.Printf("Long words: %v\n", longWords)  // [programming, awesome]
    
    // Test MapFilter - convert strings to lengths, only if length > 2
    wordLengths := MapFilter([]string{"go", "rust", "python", "c"}, func(s string) (int, bool) {
        length := len(s)
        return length, length > 2
    })
    fmt.Printf("Lengths of long words: %v\n", wordLengths)  // [4, 6]
}
```

## Generic Types and Data Structures

### 1. **Generic Data Structures**
```go
// Generic Stack
type Stack[T any] struct {
    items []T
}

func NewStack[T any]() *Stack[T] {
    return &Stack[T]{
        items: make([]T, 0),
    }
}

func (s *Stack[T]) Push(item T) {
    s.items = append(s.items, item)
}

func (s *Stack[T]) Pop() (T, bool) {
    var zero T
    if len(s.items) == 0 {
        return zero, false
    }
    
    index := len(s.items) - 1
    item := s.items[index]
    s.items = s.items[:index]
    return item, true
}

func (s *Stack[T]) Peek() (T, bool) {
    var zero T
    if len(s.items) == 0 {
        return zero, false
    }
    return s.items[len(s.items)-1], true
}

func (s *Stack[T]) IsEmpty() bool {
    return len(s.items) == 0
}

func (s *Stack[T]) Size() int {
    return len(s.items)
}

// Generic Queue
type Queue[T any] struct {
    items []T
}

func NewQueue[T any]() *Queue[T] {
    return &Queue[T]{
        items: make([]T, 0),
    }
}

func (q *Queue[T]) Enqueue(item T) {
    q.items = append(q.items, item)
}

func (q *Queue[T]) Dequeue() (T, bool) {
    var zero T
    if len(q.items) == 0 {
        return zero, false
    }
    
    item := q.items[0]
    q.items = q.items[1:]
    return item, true
}

func (q *Queue[T]) Front() (T, bool) {
    var zero T
    if len(q.items) == 0 {
        return zero, false
    }
    return q.items[0], true
}

func (q *Queue[T]) IsEmpty() bool {
    return len(q.items) == 0
}

func (q *Queue[T]) Size() int {
    return len(q.items)
}

// Generic LinkedList Node
type ListNode[T any] struct {
    Value T
    Next  *ListNode[T]
}

type LinkedList[T any] struct {
    Head *ListNode[T]
    Tail *ListNode[T]
    size int
}

func NewLinkedList[T any]() *LinkedList[T] {
    return &LinkedList[T]{}
}

func (ll *LinkedList[T]) Append(value T) {
    newNode := &ListNode[T]{Value: value}
    
    if ll.Head == nil {
        ll.Head = newNode
        ll.Tail = newNode
    } else {
        ll.Tail.Next = newNode
        ll.Tail = newNode
    }
    ll.size++
}

func (ll *LinkedList[T]) Prepend(value T) {
    newNode := &ListNode[T]{Value: value, Next: ll.Head}
    ll.Head = newNode
    
    if ll.Tail == nil {
        ll.Tail = newNode
    }
    ll.size++
}

func (ll *LinkedList[T]) Remove(value T) bool {
    if ll.Head == nil {
        return false
    }
    
    // Check if it's comparable (this would need reflection in real code)
    // For simplicity, we'll assume T is comparable
    
    // Remove from head
    if any(ll.Head.Value) == any(value) {
        ll.Head = ll.Head.Next
        if ll.Head == nil {
            ll.Tail = nil
        }
        ll.size--
        return true
    }
    
    // Remove from middle or end
    current := ll.Head
    for current.Next != nil {
        if any(current.Next.Value) == any(value) {
            if current.Next == ll.Tail {
                ll.Tail = current
            }
            current.Next = current.Next.Next
            ll.size--
            return true
        }
        current = current.Next
    }
    
    return false
}

func (ll *LinkedList[T]) Size() int {
    return ll.size
}

func (ll *LinkedList[T]) ToSlice() []T {
    result := make([]T, 0, ll.size)
    current := ll.Head
    for current != nil {
        result = append(result, current.Value)
        current = current.Next
    }
    return result
}

func main() {
    // Test Stack
    fmt.Println("Testing Stack:")
    intStack := NewStack[int]()
    intStack.Push(1)
    intStack.Push(2)
    intStack.Push(3)
    
    fmt.Printf("Stack size: %d\n", intStack.Size())
    
    for !intStack.IsEmpty() {
        if value, ok := intStack.Pop(); ok {
            fmt.Printf("Popped: %d\n", value)
        }
    }
    
    // Test Stack with strings
    stringStack := NewStack[string]()
    stringStack.Push("first")
    stringStack.Push("second")
    stringStack.Push("third")
    
    if top, ok := stringStack.Peek(); ok {
        fmt.Printf("Top of string stack: %s\n", top)
    }
    
    fmt.Println("\nTesting Queue:")
    
    // Test Queue
    intQueue := NewQueue[int]()
    intQueue.Enqueue(10)
    intQueue.Enqueue(20)
    intQueue.Enqueue(30)
    
    fmt.Printf("Queue size: %d\n", intQueue.Size())
    
    for !intQueue.IsEmpty() {
        if value, ok := intQueue.Dequeue(); ok {
            fmt.Printf("Dequeued: %d\n", value)
        }
    }
    
    fmt.Println("\nTesting LinkedList:")
    
    // Test LinkedList
    stringList := NewLinkedList[string]()
    stringList.Append("first")
    stringList.Append("second")
    stringList.Append("third")
    stringList.Prepend("zeroth")
    
    fmt.Printf("List contents: %v\n", stringList.ToSlice())
    fmt.Printf("List size: %d\n", stringList.Size())
    
    stringList.Remove("second")
    fmt.Printf("After removing 'second': %v\n", stringList.ToSlice())
}
```

### 2. **Generic Map and Set**
```go
// Generic Set
type Set[T comparable] struct {
    items map[T]struct{}
}

func NewSet[T comparable]() *Set[T] {
    return &Set[T]{
        items: make(map[T]struct{}),
    }
}

func (s *Set[T]) Add(item T) {
    s.items[item] = struct{}{}
}

func (s *Set[T]) Remove(item T) {
    delete(s.items, item)
}

func (s *Set[T]) Contains(item T) bool {
    _, exists := s.items[item]
    return exists
}

func (s *Set[T]) Size() int {
    return len(s.items)
}

func (s *Set[T]) IsEmpty() bool {
    return len(s.items) == 0
}

func (s *Set[T]) ToSlice() []T {
    result := make([]T, 0, len(s.items))
    for item := range s.items {
        result = append(result, item)
    }
    return result
}

func (s *Set[T]) Union(other *Set[T]) *Set[T] {
    result := NewSet[T]()
    
    for item := range s.items {
        result.Add(item)
    }
    
    for item := range other.items {
        result.Add(item)
    }
    
    return result
}

func (s *Set[T]) Intersection(other *Set[T]) *Set[T] {
    result := NewSet[T]()
    
    for item := range s.items {
        if other.Contains(item) {
            result.Add(item)
        }
    }
    
    return result
}

func (s *Set[T]) Difference(other *Set[T]) *Set[T] {
    result := NewSet[T]()
    
    for item := range s.items {
        if !other.Contains(item) {
            result.Add(item)
        }
    }
    
    return result
}

// Generic OrderedMap (maintains insertion order)
type OrderedMap[K comparable, V any] struct {
    keys   []K
    values map[K]V
}

func NewOrderedMap[K comparable, V any]() *OrderedMap[K, V] {
    return &OrderedMap[K, V]{
        keys:   make([]K, 0),
        values: make(map[K]V),
    }
}

func (om *OrderedMap[K, V]) Set(key K, value V) {
    if _, exists := om.values[key]; !exists {
        om.keys = append(om.keys, key)
    }
    om.values[key] = value
}

func (om *OrderedMap[K, V]) Get(key K) (V, bool) {
    value, exists := om.values[key]
    return value, exists
}

func (om *OrderedMap[K, V]) Delete(key K) {
    if _, exists := om.values[key]; exists {
        delete(om.values, key)
        
        // Remove from keys slice
        for i, k := range om.keys {
            if k == key {
                om.keys = append(om.keys[:i], om.keys[i+1:]...)
                break
            }
        }
    }
}

func (om *OrderedMap[K, V]) Keys() []K {
    result := make([]K, len(om.keys))
    copy(result, om.keys)
    return result
}

func (om *OrderedMap[K, V]) Values() []V {
    result := make([]V, len(om.keys))
    for i, key := range om.keys {
        result[i] = om.values[key]
    }
    return result
}

func (om *OrderedMap[K, V]) Size() int {
    return len(om.keys)
}

func (om *OrderedMap[K, V]) IsEmpty() bool {
    return len(om.keys) == 0
}

func (om *OrderedMap[K, V]) ForEach(fn func(K, V)) {
    for _, key := range om.keys {
        fn(key, om.values[key])
    }
}

func main() {
    // Test Set
    fmt.Println("Testing Set:")
    
    intSet := NewSet[int]()
    intSet.Add(1)
    intSet.Add(2)
    intSet.Add(3)
    intSet.Add(2)  // Duplicate, should not change set
    
    fmt.Printf("Set contents: %v\n", intSet.ToSlice())
    fmt.Printf("Set size: %d\n", intSet.Size())
    fmt.Printf("Contains 2: %t\n", intSet.Contains(2))
    fmt.Printf("Contains 5: %t\n", intSet.Contains(5))
    
    intSet.Remove(2)
    fmt.Printf("After removing 2: %v\n", intSet.ToSlice())
    
    // Set operations
    set1 := NewSet[string]()
    set1.Add("apple")
    set1.Add("banana")
    set1.Add("cherry")
    
    set2 := NewSet[string]()
    set2.Add("banana")
    set2.Add("date")
    set2.Add("elderberry")
    
    union := set1.Union(set2)
    intersection := set1.Intersection(set2)
    difference := set1.Difference(set2)
    
    fmt.Printf("\nSet1: %v\n", set1.ToSlice())
    fmt.Printf("Set2: %v\n", set2.ToSlice())
    fmt.Printf("Union: %v\n", union.ToSlice())
    fmt.Printf("Intersection: %v\n", intersection.ToSlice())
    fmt.Printf("Set1 - Set2: %v\n", difference.ToSlice())
    
    // Test OrderedMap
    fmt.Println("\nTesting OrderedMap:")
    
    orderedMap := NewOrderedMap[string, int]()
    orderedMap.Set("first", 1)
    orderedMap.Set("second", 2)
    orderedMap.Set("third", 3)
    orderedMap.Set("second", 22)  // Update existing key
    
    fmt.Printf("Keys in order: %v\n", orderedMap.Keys())
    fmt.Printf("Values in order: %v\n", orderedMap.Values())
    
    if value, exists := orderedMap.Get("second"); exists {
        fmt.Printf("Value for 'second': %d\n", value)
    }
    
    fmt.Println("\nIterating over OrderedMap:")
    orderedMap.ForEach(func(key string, value int) {
        fmt.Printf("  %s: %d\n", key, value)
    })
    
    orderedMap.Delete("second")
    fmt.Printf("After deleting 'second': %v\n", orderedMap.Keys())
}
```

## Advanced Generic Patterns

### 1. **Generic Interfaces and Type Sets**
```go
// Type set interface (Go 1.18+)
type Number interface {
    int | int32 | int64 | float32 | float64
}

type Stringable interface {
    string | fmt.Stringer
}

// Interface with methods and type constraints
type Comparable[T any] interface {
    Compare(T) int
    ~T  // Type constraint: underlying type must be T
}

// Generic function with interface constraint
func Sort[T Number](slice []T) {
    // Simple bubble sort for demonstration
    n := len(slice)
    for i := 0; i < n-1; i++ {
        for j := 0; j < n-1-i; j++ {
            if slice[j] > slice[j+1] {
                slice[j], slice[j+1] = slice[j+1], slice[j]
            }
        }
    }
}

// Generic interface for collections
type Collection[T any] interface {
    Add(T)
    Remove(T) bool
    Contains(T) bool
    Size() int
    IsEmpty() bool
    ToSlice() []T
}

// Generic function that works with any collection
func PrintCollection[T any, C Collection[T]](collection C, name string) {
    fmt.Printf("%s (size: %d): %v\n", name, collection.Size(), collection.ToSlice())
}

// Version type that implements custom comparison
type Version struct {
    Major, Minor, Patch int
}

func (v Version) Compare(other Version) int {
    if v.Major != other.Major {
        return v.Major - other.Major
    }
    if v.Minor != other.Minor {
        return v.Minor - other.Minor
    }
    return v.Patch - other.Patch
}

func (v Version) String() string {
    return fmt.Sprintf("%d.%d.%d", v.Major, v.Minor, v.Patch)
}

// Generic function for custom comparable types
func Max[T comparable](a, b T) T {
    // For demonstration - in real code you'd need proper comparison
    if fmt.Sprintf("%v", a) > fmt.Sprintf("%v", b) {
        return a
    }
    return b
}

// Generic function with method constraint
func FormatSlice[T fmt.Stringer](items []T) []string {
    result := make([]string, len(items))
    for i, item := range items {
        result[i] = item.String()
    }
    return result
}

// Generic Result type (like Rust's Result)
type Result[T, E any] struct {
    value T
    err   E
    isOk  bool
}

func Ok[T, E any](value T) Result[T, E] {
    return Result[T, E]{value: value, isOk: true}
}

func Err[T, E any](err E) Result[T, E] {
    return Result[T, E]{err: err, isOk: false}
}

func (r Result[T, E]) IsOk() bool {
    return r.isOk
}

func (r Result[T, E]) IsErr() bool {
    return !r.isOk
}

func (r Result[T, E]) Unwrap() T {
    if !r.isOk {
        panic("called Unwrap on error result")
    }
    return r.value
}

func (r Result[T, E]) UnwrapOr(defaultValue T) T {
    if r.isOk {
        return r.value
    }
    return defaultValue
}

func (r Result[T, E]) Error() E {
    if r.isOk {
        panic("called Error on ok result")
    }
    return r.err
}

// Function that returns Result
func SafeDivide(a, b float64) Result[float64, string] {
    if b == 0 {
        return Err[float64, string]("division by zero")
    }
    return Ok[float64, string](a / b)
}

func main() {
    // Test Number interface with Sort
    fmt.Println("Testing Number interface:")
    
    intSlice := []int{5, 2, 8, 1, 9, 3}
    fmt.Printf("Before sort: %v\n", intSlice)
    Sort(intSlice)
    fmt.Printf("After sort: %v\n", intSlice)
    
    floatSlice := []float64{3.14, 1.41, 2.71, 0.57}
    fmt.Printf("Before sort: %v\n", floatSlice)
    Sort(floatSlice)
    fmt.Printf("After sort: %v\n", floatSlice)
    
    // Test Version comparison
    fmt.Println("\nTesting Version:")
    v1 := Version{1, 2, 3}
    v2 := Version{1, 2, 4}
    v3 := Version{2, 0, 0}
    
    fmt.Printf("%s vs %s: %d\n", v1, v2, v1.Compare(v2))  // -1
    fmt.Printf("%s vs %s: %d\n", v2, v1, v2.Compare(v1))  // 1
    fmt.Printf("%s vs %s: %d\n", v1, v3, v1.Compare(v3))  // -1
    
    // Test FormatSlice with Stringer interface
    versions := []Version{v1, v2, v3}
    formatted := FormatSlice(versions)
    fmt.Printf("Formatted versions: %v\n", formatted)
    
    // Test Result type
    fmt.Println("\nTesting Result type:")
    
    results := []Result[float64, string]{
        SafeDivide(10, 2),
        SafeDivide(10, 0),
        SafeDivide(7, 3),
    }
    
    for i, result := range results {
        fmt.Printf("Result %d: ", i+1)
        if result.IsOk() {
            fmt.Printf("Ok(%.2f)\n", result.Unwrap())
        } else {
            fmt.Printf("Err(%s)\n", result.Error())
        }
    }
    
    // Using UnwrapOr for safe default values
    failedResult := SafeDivide(5, 0)
    safeValue := failedResult.UnwrapOr(0.0)
    fmt.Printf("Safe value with default: %.2f\n", safeValue)
}
```

### 2. **Generic Factory and Builder Patterns**
```go
// Generic Factory interface
type Factory[T any] interface {
    Create() T
    CreateWithParams(params map[string]interface{}) T
}

// Generic Builder interface
type Builder[T any] interface {
    Build() T
    Reset() Builder[T]
}

// Generic Repository pattern
type Repository[T any, ID comparable] interface {
    Save(entity T) error
    FindByID(id ID) (T, error)
    FindAll() ([]T, error)
    Delete(id ID) error
}

// In-memory repository implementation
type InMemoryRepository[T any, ID comparable] struct {
    data   map[ID]T
    nextID func() ID
    getID  func(T) ID
}

func NewInMemoryRepository[T any, ID comparable](
    getID func(T) ID,
    nextID func() ID,
) *InMemoryRepository[T, ID] {
    return &InMemoryRepository[T, ID]{
        data:   make(map[ID]T),
        nextID: nextID,
        getID:  getID,
    }
}

func (r *InMemoryRepository[T, ID]) Save(entity T) error {
    id := r.getID(entity)
    r.data[id] = entity
    return nil
}

func (r *InMemoryRepository[T, ID]) FindByID(id ID) (T, error) {
    var zero T
    entity, exists := r.data[id]
    if !exists {
        return zero, fmt.Errorf("entity with ID %v not found", id)
    }
    return entity, nil
}

func (r *InMemoryRepository[T, ID]) FindAll() ([]T, error) {
    entities := make([]T, 0, len(r.data))
    for _, entity := range r.data {
        entities = append(entities, entity)
    }
    return entities, nil
}

func (r *InMemoryRepository[T, ID]) Delete(id ID) error {
    if _, exists := r.data[id]; !exists {
        return fmt.Errorf("entity with ID %v not found", id)
    }
    delete(r.data, id)
    return nil
}

// Example entities
type User struct {
    ID    int
    Name  string
    Email string
}

type Product struct {
    SKU         string
    Name        string
    Price       float64
    Description string
}

// Generic Service layer
type Service[T any, ID comparable] struct {
    repo Repository[T, ID]
}

func NewService[T any, ID comparable](repo Repository[T, ID]) *Service[T, ID] {
    return &Service[T, ID]{repo: repo}
}

func (s *Service[T, ID]) Create(entity T) error {
    return s.repo.Save(entity)
}

func (s *Service[T, ID]) GetByID(id ID) (T, error) {
    return s.repo.FindByID(id)
}

func (s *Service[T, ID]) GetAll() ([]T, error) {
    return s.repo.FindAll()
}

func (s *Service[T, ID]) Update(entity T) error {
    return s.repo.Save(entity)  // In this simple impl, save = update
}

func (s *Service[T, ID]) Delete(id ID) error {
    return s.repo.Delete(id)
}

// Generic Cache
type Cache[K comparable, V any] struct {
    data map[K]V
    ttl  time.Duration
    timestamps map[K]time.Time
}

func NewCache[K comparable, V any](ttl time.Duration) *Cache[K, V] {
    return &Cache[K, V]{
        data:       make(map[K]V),
        ttl:        ttl,
        timestamps: make(map[K]time.Time),
    }
}

func (c *Cache[K, V]) Set(key K, value V) {
    c.data[key] = value
    c.timestamps[key] = time.Now()
}

func (c *Cache[K, V]) Get(key K) (V, bool) {
    var zero V
    
    timestamp, exists := c.timestamps[key]
    if !exists {
        return zero, false
    }
    
    // Check if expired
    if time.Since(timestamp) > c.ttl {
        delete(c.data, key)
        delete(c.timestamps, key)
        return zero, false
    }
    
    value, exists := c.data[key]
    return value, exists
}

func (c *Cache[K, V]) Delete(key K) {
    delete(c.data, key)
    delete(c.timestamps, key)
}

func (c *Cache[K, V]) Clear() {
    c.data = make(map[K]V)
    c.timestamps = make(map[K]time.Time)
}

func (c *Cache[K, V]) Size() int {
    // Clean expired entries first
    now := time.Now()
    for key, timestamp := range c.timestamps {
        if now.Sub(timestamp) > c.ttl {
            delete(c.data, key)
            delete(c.timestamps, key)
        }
    }
    return len(c.data)
}

func main() {
    // Test Repository pattern
    fmt.Println("Testing Generic Repository:")
    
    userRepo := NewInMemoryRepository[User, int](
        func(u User) int { return u.ID },
        func() int { return int(time.Now().UnixNano()) },  // Simple ID generator
    )
    
    userService := NewService[User, int](userRepo)
    
    // Create users
    users := []User{
        {ID: 1, Name: "Alice", Email: "alice@example.com"},
        {ID: 2, Name: "Bob", Email: "bob@example.com"},
        {ID: 3, Name: "Charlie", Email: "charlie@example.com"},
    }
    
    for _, user := range users {
        err := userService.Create(user)
        if err != nil {
            fmt.Printf("Error creating user: %v\n", err)
        }
    }
    
    // Find user by ID
    user, err := userService.GetByID(2)
    if err != nil {
        fmt.Printf("Error finding user: %v\n", err)
    } else {
        fmt.Printf("Found user: %+v\n", user)
    }
    
    // Get all users
    allUsers, err := userService.GetAll()
    if err != nil {
        fmt.Printf("Error getting all users: %v\n", err)
    } else {
        fmt.Printf("All users: %+v\n", allUsers)
    }
    
    // Test Product repository
    fmt.Println("\nTesting Product Repository:")
    
    productRepo := NewInMemoryRepository[Product, string](
        func(p Product) string { return p.SKU },
        func() string { return fmt.Sprintf("SKU-%d", time.Now().UnixNano()) },
    )
    
    productService := NewService[Product, string](productRepo)
    
    products := []Product{
        {SKU: "LAPTOP-001", Name: "Gaming Laptop", Price: 1299.99, Description: "High-performance laptop"},
        {SKU: "PHONE-001", Name: "Smartphone", Price: 699.99, Description: "Latest smartphone"},
    }
    
    for _, product := range products {
        productService.Create(product)
    }
    
    allProducts, _ := productService.GetAll()
    fmt.Printf("All products: %+v\n", allProducts)
    
    // Test Cache
    fmt.Println("\nTesting Generic Cache:")
    
    cache := NewCache[string, int](2 * time.Second)
    
    cache.Set("key1", 100)
    cache.Set("key2", 200)
    
    fmt.Printf("Cache size: %d\n", cache.Size())
    
    if value, found := cache.Get("key1"); found {
        fmt.Printf("Found key1: %d\n", value)
    }
    
    // Wait for expiry
    time.Sleep(3 * time.Second)
    
    if _, found := cache.Get("key1"); !found {
        fmt.Println("key1 expired and not found")
    }
    
    fmt.Printf("Cache size after expiry: %d\n", cache.Size())
}
```

## Performance Considerations

### 1. **Generic Performance Patterns**
```go
import (
    "testing"
    "time"
)

// Generic vs Interface performance comparison
type Adder interface {
    Add(int) int
}

type IntAdder struct {
    value int
}

func (ia *IntAdder) Add(x int) int {
    ia.value += x
    return ia.value
}

// Generic function - compile-time type resolution
func GenericAdd[T constraints.Integer](a, b T) T {
    return a + b
}

// Interface function - runtime type resolution
func InterfaceAdd(adder Adder, x int) int {
    return adder.Add(x)
}

// Specialized function - no generics
func SpecializedAdd(a, b int) int {
    return a + b
}

// Benchmark functions (would be in _test.go file)
func BenchmarkGenericAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        GenericAdd(i, i+1)
    }
}

func BenchmarkInterfaceAdd(b *testing.B) {
    adder := &IntAdder{}
    for i := 0; i < b.N; i++ {
        InterfaceAdd(adder, i)
    }
}

func BenchmarkSpecializedAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        SpecializedAdd(i, i+1)
    }
}

// Memory-efficient generic pool
type Pool[T any] struct {
    items []T
    new   func() T
}

func NewPool[T any](newFunc func() T) *Pool[T] {
    return &Pool[T]{
        items: make([]T, 0),
        new:   newFunc,
    }
}

func (p *Pool[T]) Get() T {
    if len(p.items) > 0 {
        item := p.items[len(p.items)-1]
        p.items = p.items[:len(p.items)-1]
        return item
    }
    return p.new()
}

func (p *Pool[T]) Put(item T) {
    // In real implementation, you might want to limit pool size
    p.items = append(p.items, item)
}

// Example: Buffer pool
type Buffer struct {
    data []byte
}

func (b *Buffer) Reset() {
    b.data = b.data[:0]
}

func (b *Buffer) Write(data []byte) {
    b.data = append(b.data, data...)
}

func (b *Buffer) String() string {
    return string(b.data)
}

func main() {
    // Performance demonstration
    fmt.Println("Performance Comparison Demo:")
    
    // Time generic function
    start := time.Now()
    for i := 0; i < 1000000; i++ {
        GenericAdd(i, i+1)
    }
    genericTime := time.Since(start)
    fmt.Printf("Generic function time: %v\n", genericTime)
    
    // Time specialized function
    start = time.Now()
    for i := 0; i < 1000000; i++ {
        SpecializedAdd(i, i+1)
    }
    specializedTime := time.Since(start)
    fmt.Printf("Specialized function time: %v\n", specializedTime)
    
    // Time interface function
    adder := &IntAdder{}
    start = time.Now()
    for i := 0; i < 1000000; i++ {
        InterfaceAdd(adder, i)
    }
    interfaceTime := time.Since(start)
    fmt.Printf("Interface function time: %v\n", interfaceTime)
    
    fmt.Printf("Generic vs Specialized ratio: %.2f\n", float64(genericTime)/float64(specializedTime))
    fmt.Printf("Interface vs Specialized ratio: %.2f\n", float64(interfaceTime)/float64(specializedTime))
    
    // Test Pool
    fmt.Println("\nTesting Generic Pool:")
    
    bufferPool := NewPool[*Buffer](func() *Buffer {
        return &Buffer{data: make([]byte, 0, 1024)}
    })
    
    // Get buffer from pool
    buf1 := bufferPool.Get()
    buf1.Write([]byte("Hello, "))
    buf1.Write([]byte("World!"))
    fmt.Printf("Buffer content: %s\n", buf1.String())
    
    // Return buffer to pool
    buf1.Reset()
    bufferPool.Put(buf1)
    
    // Get buffer again (should reuse the same one)
    buf2 := bufferPool.Get()
    buf2.Write([]byte("Reused buffer"))
    fmt.Printf("Reused buffer content: %s\n", buf2.String())
    
    fmt.Printf("Buffers are same object: %t\n", buf1 == buf2)
}
```

## How to Run This

```powershell
cd 19_generics
go run .
```

## Key Takeaways

1. **Type parameters enable code reuse** - write once, work with any type
2. **Type constraints limit what types can be used** - maintains type safety
3. **Interface constraints with type sets** - powerful way to restrict type parameters
4. **Generic collections avoid type casting** - compile-time type safety
5. **Performance is similar to specialized functions** - Go compiles optimized code
6. **Use generics for data structures and algorithms** - where type flexibility matters
7. **Don't over-generify** - sometimes specific types are clearer
8. **Type inference reduces verbosity** - compiler can often infer types

Generics are like having smart templates that let you write flexible, reusable code without sacrificing type safety or performance. They're perfect for data structures, algorithms, and utility functions that need to work with different types while maintaining Go's strong typing guarantees!

Note: This code requires Go 1.18 or later and may need the `golang.org/x/exp/constraints` package for some constraint types.
