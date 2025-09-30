# Goroutines - Concurrent Programming Made Simple

This folder teaches you about goroutines - Go's lightweight threads that make concurrent programming incredibly easy and powerful. Think of goroutines as super-efficient mini-threads that can run thousands simultaneously without the overhead of traditional threads. They're what makes Go perfect for building scalable, concurrent applications.

## What This Teaches You

**Goroutine Fundamentals:**
- What goroutines are and how they work
- Creating and running goroutines
- Goroutine lifecycle and scheduling
- Communication between goroutines
- Synchronization and coordination
- Common concurrency patterns
- Performance characteristics and best practices

## The Code Explained Simply

```go
package main

import (
    "fmt"
    "time"
)

// Simple function to run in a goroutine
func sayHello(name string) {
    for i := 0; i < 3; i++ {
        fmt.Printf("Hello, %s! (%d)\n", name, i+1)
        time.Sleep(100 * time.Millisecond) // Small delay
    }
}

// Function that does some work
func doWork(id int) {
    fmt.Printf("Worker %d starting\n", id)
    time.Sleep(500 * time.Millisecond) // Simulate work
    fmt.Printf("Worker %d done\n", id)
}

// Function that counts numbers
func countNumbers(from, to int, name string) {
    for i := from; i <= to; i++ {
        fmt.Printf("%s: %d\n", name, i)
        time.Sleep(50 * time.Millisecond)
    }
}

func main() {
    fmt.Println("=== Basic Goroutines ===")
    
    // Start a goroutine - runs concurrently with main
    go sayHello("Alice")
    
    // Start another goroutine
    go sayHello("Bob")
    
    // Main goroutine does its own work
    sayHello("Main")
    
    // Give goroutines time to finish
    time.Sleep(1 * time.Second)
    
    fmt.Println("\n=== Multiple Workers ===")
    
    // Start multiple worker goroutines
    for i := 1; i <= 3; i++ {
        go doWork(i)
    }
    
    // Wait for workers to finish
    time.Sleep(1 * time.Second)
    
    fmt.Println("\n=== Concurrent Counting ===")
    
    // Two goroutines counting simultaneously
    go countNumbers(1, 5, "Counter-A")
    go countNumbers(10, 15, "Counter-B")
    
    // Wait for counting to finish
    time.Sleep(2 * time.Second)
    
    fmt.Println("\nMain goroutine finished!")
}
```

## Basic Goroutine Patterns

### 1. **Simple Goroutine Creation**
```go
import (
    "fmt"
    "runtime"
    "sync"
    "time"
)

// Function to be run in goroutine
func printMessage(message string, delay time.Duration) {
    for i := 0; i < 3; i++ {
        fmt.Printf("%s - iteration %d\n", message, i+1)
        time.Sleep(delay)
    }
}

// Function that returns a value
func calculate(n int) int {
    time.Sleep(100 * time.Millisecond) // Simulate work
    return n * n
}

// Function with multiple return values
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("division by zero")
    }
    time.Sleep(50 * time.Millisecond) // Simulate work
    return a / b, nil
}

func basicGoroutineExamples() {
    fmt.Println("=== Basic Goroutine Examples ===")
    
    // Method 1: goroutine with function call
    go printMessage("Goroutine-1", 200*time.Millisecond)
    go printMessage("Goroutine-2", 150*time.Millisecond)
    
    // Method 2: goroutine with anonymous function
    go func() {
        fmt.Println("Anonymous goroutine started")
        time.Sleep(300 * time.Millisecond)
        fmt.Println("Anonymous goroutine finished")
    }()
    
    // Method 3: goroutine with closure
    message := "Closure message"
    go func() {
        fmt.Printf("From closure: %s\n", message)
        time.Sleep(100 * time.Millisecond)
    }()
    
    // Method 4: goroutine with parameters
    go func(msg string, times int) {
        for i := 0; i < times; i++ {
            fmt.Printf("Parameterized: %s (%d)\n", msg, i+1)
            time.Sleep(80 * time.Millisecond)
        }
    }("Parameter message", 2)
    
    // Main goroutine continues
    fmt.Println("Main goroutine is also working...")
    time.Sleep(100 * time.Millisecond)
    fmt.Println("Main goroutine work done!")
    
    // Wait for all goroutines to complete
    time.Sleep(1 * time.Second)
    
    fmt.Printf("Number of goroutines: %d\n", runtime.NumGoroutine())
}
```

### 2. **Goroutines with Channels for Communication**
```go
// Channel examples for goroutine communication
func channelCommunication() {
    fmt.Println("\n=== Goroutine Communication with Channels ===")
    
    // Create a channel for communication
    resultChannel := make(chan int)
    
    // Start a goroutine that sends a result
    go func() {
        result := calculate(5)
        resultChannel <- result // Send result to channel
    }()
    
    // Receive result from channel
    result := <-resultChannel
    fmt.Printf("Received result: %d\n", result)
    
    // Multiple goroutines with channel
    numbers := []int{1, 2, 3, 4, 5}
    squares := make(chan int)
    
    // Start goroutines for each number
    for _, num := range numbers {
        go func(n int) {
            squares <- n * n
        }(num)
    }
    
    // Receive results
    fmt.Println("Squares:")
    for i := 0; i < len(numbers); i++ {
        square := <-squares
        fmt.Printf("Square: %d\n", square)
    }
    
    // Bidirectional communication
    jobs := make(chan int)
    results := make(chan int)
    
    // Worker goroutine
    go func() {
        for job := range jobs {
            results <- job * 2 // Double the number
        }
        close(results)
    }()
    
    // Send jobs
    go func() {
        for i := 1; i <= 3; i++ {
            jobs <- i
        }
        close(jobs)
    }()
    
    // Receive results
    fmt.Println("Job results:")
    for result := range results {
        fmt.Printf("Result: %d\n", result)
    }
}
```

### 3. **WaitGroup for Synchronization**
```go
// Using WaitGroup to wait for multiple goroutines
func waitGroupExample() {
    fmt.Println("\n=== WaitGroup Example ===")
    
    var wg sync.WaitGroup
    
    // Number of goroutines to wait for
    numWorkers := 3
    wg.Add(numWorkers)
    
    for i := 1; i <= numWorkers; i++ {
        go func(workerID int) {
            defer wg.Done() // Signal completion when function exits
            
            fmt.Printf("Worker %d starting\n", workerID)
            
            // Simulate work with random duration
            workTime := time.Duration(workerID*100) * time.Millisecond
            time.Sleep(workTime)
            
            fmt.Printf("Worker %d completed work in %v\n", workerID, workTime)
        }(i)
    }
    
    fmt.Println("Waiting for all workers to complete...")
    wg.Wait() // Block until all goroutines call Done()
    fmt.Println("All workers completed!")
    
    // Advanced WaitGroup usage
    fmt.Println("\n=== Advanced WaitGroup Usage ===")
    
    tasks := []string{"task1", "task2", "task3", "task4", "task5"}
    var wg2 sync.WaitGroup
    
    for _, task := range tasks {
        wg2.Add(1)
        go func(taskName string) {
            defer wg2.Done()
            processTask(taskName)
        }(task)
    }
    
    wg2.Wait()
    fmt.Println("All tasks processed!")
}

func processTask(taskName string) {
    fmt.Printf("Processing %s...\n", taskName)
    time.Sleep(time.Duration(len(taskName)*50) * time.Millisecond)
    fmt.Printf("Completed %s\n", taskName)
}
```

## Advanced Goroutine Patterns

### 1. **Worker Pool Pattern**
```go
// Worker pool for processing jobs
type Job struct {
    ID   int
    Data string
}

type Result struct {
    Job    Job
    Output string
    Error  error
}

func workerPoolExample() {
    fmt.Println("\n=== Worker Pool Pattern ===")
    
    const numWorkers = 3
    const numJobs = 10
    
    jobs := make(chan Job, numJobs)
    results := make(chan Result, numJobs)
    
    // Start workers
    var wg sync.WaitGroup
    for i := 1; i <= numWorkers; i++ {
        wg.Add(1)
        go worker(i, jobs, results, &wg)
    }
    
    // Send jobs
    go func() {
        for i := 1; i <= numJobs; i++ {
            jobs <- Job{
                ID:   i,
                Data: fmt.Sprintf("job-data-%d", i),
            }
        }
        close(jobs)
    }()
    
    // Close results channel when all workers are done
    go func() {
        wg.Wait()
        close(results)
    }()
    
    // Collect results
    fmt.Println("Results:")
    for result := range results {
        if result.Error != nil {
            fmt.Printf("Job %d failed: %v\n", result.Job.ID, result.Error)
        } else {
            fmt.Printf("Job %d result: %s\n", result.Job.ID, result.Output)
        }
    }
    
    fmt.Println("Worker pool completed!")
}

func worker(id int, jobs <-chan Job, results chan<- Result, wg *sync.WaitGroup) {
    defer wg.Done()
    
    for job := range jobs {
        fmt.Printf("Worker %d processing job %d\n", id, job.ID)
        
        // Simulate processing time
        time.Sleep(time.Duration(job.ID*100) * time.Millisecond)
        
        // Process job
        output := fmt.Sprintf("processed-%s-by-worker-%d", job.Data, id)
        
        results <- Result{
            Job:    job,
            Output: output,
            Error:  nil,
        }
        
        fmt.Printf("Worker %d completed job %d\n", id, job.ID)
    }
    
    fmt.Printf("Worker %d finished\n", id)
}
```

### 2. **Producer-Consumer Pattern**
```go
// Producer-Consumer pattern with buffered channels
func producerConsumerExample() {
    fmt.Println("\n=== Producer-Consumer Pattern ===")
    
    // Buffered channel acts as a queue
    queue := make(chan string, 5)
    
    var wg sync.WaitGroup
    
    // Start multiple producers
    numProducers := 2
    for i := 1; i <= numProducers; i++ {
        wg.Add(1)
        go producer(i, queue, &wg)
    }
    
    // Start multiple consumers
    numConsumers := 3
    for i := 1; i <= numConsumers; i++ {
        wg.Add(1)
        go consumer(i, queue, &wg)
    }
    
    // Close the queue when all producers are done
    go func() {
        // Wait for producers only
        var producerWG sync.WaitGroup
        producerWG.Add(numProducers)
        
        // This is a simplified example - in practice you'd coordinate this better
        time.Sleep(2 * time.Second) // Let producers run
        close(queue)
    }()
    
    wg.Wait()
    fmt.Println("Producer-Consumer example completed!")
}

func producer(id int, queue chan<- string, wg *sync.WaitGroup) {
    defer wg.Done()
    
    for i := 1; i <= 3; i++ {
        item := fmt.Sprintf("item-%d-from-producer-%d", i, id)
        
        fmt.Printf("Producer %d producing: %s\n", id, item)
        queue <- item
        
        // Simulate production time
        time.Sleep(200 * time.Millisecond)
    }
    
    fmt.Printf("Producer %d finished\n", id)
}

func consumer(id int, queue <-chan string, wg *sync.WaitGroup) {
    defer wg.Done()
    
    for item := range queue {
        fmt.Printf("Consumer %d consuming: %s\n", id, item)
        
        // Simulate consumption time
        time.Sleep(300 * time.Millisecond)
        
        fmt.Printf("Consumer %d finished: %s\n", id, item)
    }
    
    fmt.Printf("Consumer %d finished\n", id)
}
```

### 3. **Fan-out/Fan-in Pattern**
```go
// Fan-out: distribute work to multiple goroutines
// Fan-in: collect results from multiple goroutines
func fanOutFanInExample() {
    fmt.Println("\n=== Fan-out/Fan-in Pattern ===")
    
    // Input data
    numbers := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
    
    // Fan-out: create input channel and distribute work
    inputCh := make(chan int)
    
    // Start the input producer
    go func() {
        for _, num := range numbers {
            inputCh <- num
        }
        close(inputCh)
    }()
    
    // Fan-out: create multiple workers
    numWorkers := 3
    workerChannels := make([]<-chan int, numWorkers)
    
    for i := 0; i < numWorkers; i++ {
        workerChannels[i] = squareWorker(inputCh)
    }
    
    // Fan-in: merge all worker outputs
    resultCh := mergeChannels(workerChannels...)
    
    // Collect all results
    fmt.Println("Results:")
    var results []int
    for result := range resultCh {
        results = append(results, result)
        fmt.Printf("Result: %d\n", result)
    }
    
    fmt.Printf("Total results collected: %d\n", len(results))
}

// Worker that squares numbers
func squareWorker(input <-chan int) <-chan int {
    output := make(chan int)
    
    go func() {
        defer close(output)
        
        for num := range input {
            // Simulate work
            time.Sleep(100 * time.Millisecond)
            
            square := num * num
            fmt.Printf("Worker squared %d = %d\n", num, square)
            
            output <- square
        }
    }()
    
    return output
}

// Fan-in: merge multiple channels into one
func mergeChannels(channels ...<-chan int) <-chan int {
    out := make(chan int)
    var wg sync.WaitGroup
    
    // Start a goroutine for each input channel
    for _, ch := range channels {
        wg.Add(1)
        go func(c <-chan int) {
            defer wg.Done()
            for val := range c {
                out <- val
            }
        }(ch)
    }
    
    // Close output channel when all input channels are closed
    go func() {
        wg.Wait()
        close(out)
    }()
    
    return out
}
```

## Goroutine Synchronization Primitives

### 1. **Mutex for Shared State**
```go
import "sync"

// Shared counter with mutex protection
type SafeCounter struct {
    mu    sync.Mutex
    count int
}

func (c *SafeCounter) Increment() {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.count++
}

func (c *SafeCounter) Decrement() {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.count--
}

func (c *SafeCounter) Value() int {
    c.mu.Lock()
    defer c.mu.Unlock()
    return c.count
}

// Unsafe counter for comparison
type UnsafeCounter struct {
    count int
}

func (c *UnsafeCounter) Increment() {
    c.count++
}

func (c *UnsafeCounter) Value() int {
    return c.count
}

func mutexExample() {
    fmt.Println("\n=== Mutex Example ===")
    
    // Safe counter with mutex
    safeCounter := &SafeCounter{}
    
    var wg sync.WaitGroup
    numGoroutines := 10
    incrementsPerGoroutine := 100
    
    // Start goroutines that increment the safe counter
    for i := 0; i < numGoroutines; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            for j := 0; j < incrementsPerGoroutine; j++ {
                safeCounter.Increment()
            }
            fmt.Printf("Goroutine %d completed safe increments\n", id)
        }(i)
    }
    
    wg.Wait()
    expectedValue := numGoroutines * incrementsPerGoroutine
    actualValue := safeCounter.Value()
    
    fmt.Printf("Safe counter - Expected: %d, Actual: %d, Correct: %t\n", 
        expectedValue, actualValue, expectedValue == actualValue)
    
    // Demonstrate race condition with unsafe counter
    fmt.Println("\n=== Race Condition Demo (Unsafe Counter) ===")
    
    unsafeCounter := &UnsafeCounter{}
    
    // Reset WaitGroup
    wg = sync.WaitGroup{}
    
    for i := 0; i < numGoroutines; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            for j := 0; j < incrementsPerGoroutine; j++ {
                unsafeCounter.Increment()
            }
        }(i)
    }
    
    wg.Wait()
    unsafeActualValue := unsafeCounter.Value()
    
    fmt.Printf("Unsafe counter - Expected: %d, Actual: %d, Correct: %t\n", 
        expectedValue, unsafeActualValue, expectedValue == unsafeActualValue)
    
    if expectedValue != unsafeActualValue {
        fmt.Println("Race condition detected! Values don't match due to concurrent access.")
    }
}
```

### 2. **RWMutex for Read-Heavy Workloads**
```go
// Cache with RWMutex for concurrent reads
type Cache struct {
    mu   sync.RWMutex
    data map[string]string
}

func NewCache() *Cache {
    return &Cache{
        data: make(map[string]string),
    }
}

func (c *Cache) Set(key, value string) {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.data[key] = value
    fmt.Printf("Set %s = %s\n", key, value)
}

func (c *Cache) Get(key string) (string, bool) {
    c.mu.RLock()
    defer c.mu.RUnlock()
    value, exists := c.data[key]
    if exists {
        fmt.Printf("Get %s = %s\n", key, value)
    } else {
        fmt.Printf("Get %s = not found\n", key)
    }
    return value, exists
}

func (c *Cache) Delete(key string) {
    c.mu.Lock()
    defer c.mu.Unlock()
    delete(c.data, key)
    fmt.Printf("Deleted %s\n", key)
}

func (c *Cache) Size() int {
    c.mu.RLock()
    defer c.mu.RUnlock()
    return len(c.data)
}

func rwMutexExample() {
    fmt.Println("\n=== RWMutex Example ===")
    
    cache := NewCache()
    
    var wg sync.WaitGroup
    
    // Writer goroutines (fewer)
    numWriters := 2
    for i := 0; i < numWriters; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            for j := 0; j < 3; j++ {
                key := fmt.Sprintf("key-%d-%d", id, j)
                value := fmt.Sprintf("value-%d-%d", id, j)
                cache.Set(key, value)
                time.Sleep(50 * time.Millisecond)
            }
        }(i)
    }
    
    // Reader goroutines (more)
    numReaders := 5
    for i := 0; i < numReaders; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            for j := 0; j < 5; j++ {
                key := fmt.Sprintf("key-%d-%d", id%numWriters, j%3)
                cache.Get(key)
                time.Sleep(20 * time.Millisecond)
            }
        }(i)
    }
    
    wg.Wait()
    fmt.Printf("Final cache size: %d\n", cache.Size())
}
```

## Real-World Goroutine Examples

### 1. **HTTP Server with Concurrent Request Handling**
```go
import (
    "fmt"
    "net/http"
    "sync/atomic"
    "time"
)

var requestCounter int64

func httpServerExample() {
    fmt.Println("\n=== HTTP Server Example ===")
    
    // Handler function - each request runs in its own goroutine
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        // Increment request counter atomically
        reqNum := atomic.AddInt64(&requestCounter, 1)
        
        fmt.Printf("Handling request %d from %s\n", reqNum, r.RemoteAddr)
        
        // Simulate processing time
        time.Sleep(100 * time.Millisecond)
        
        fmt.Fprintf(w, "Hello! Request #%d processed at %s\n", 
            reqNum, time.Now().Format("15:04:05"))
    })
    
    http.HandleFunc("/slow", func(w http.ResponseWriter, r *http.Request) {
        reqNum := atomic.AddInt64(&requestCounter, 1)
        fmt.Printf("Handling slow request %d\n", reqNum)
        
        // Simulate slow processing
        time.Sleep(2 * time.Second)
        
        fmt.Fprintf(w, "Slow request #%d completed at %s\n", 
            reqNum, time.Now().Format("15:04:05"))
    })
    
    http.HandleFunc("/stats", func(w http.ResponseWriter, r *http.Request) {
        count := atomic.LoadInt64(&requestCounter)
        fmt.Fprintf(w, "Total requests processed: %d\n", count)
    })
    
    fmt.Println("Starting server on :8080")
    fmt.Println("Try:")
    fmt.Println("  curl http://localhost:8080/")
    fmt.Println("  curl http://localhost:8080/slow")
    fmt.Println("  curl http://localhost:8080/stats")
    
    // This would start the server (commented out for demo)
    // log.Fatal(http.ListenAndServe(":8080", nil))
    
    // For demo, we'll simulate concurrent requests
    simulateConcurrentRequests()
}

func simulateConcurrentRequests() {
    fmt.Println("Simulating concurrent request processing...")
    
    var wg sync.WaitGroup
    
    // Simulate 5 concurrent requests
    for i := 1; i <= 5; i++ {
        wg.Add(1)
        go func(requestID int) {
            defer wg.Done()
            
            reqNum := atomic.AddInt64(&requestCounter, 1)
            fmt.Printf("Processing simulated request %d (total: %d)\n", requestID, reqNum)
            
            // Simulate request processing
            processingTime := time.Duration(requestID*50) * time.Millisecond
            time.Sleep(processingTime)
            
            fmt.Printf("Completed simulated request %d in %v\n", requestID, processingTime)
        }(i)
    }
    
    wg.Wait()
    fmt.Printf("All requests completed. Total: %d\n", atomic.LoadInt64(&requestCounter))
}
```

### 2. **Background Task Processing**
```go
// Task processor with graceful shutdown
type TaskProcessor struct {
    tasks       chan Task
    workers     int
    stopCh      chan struct{}
    wg          sync.WaitGroup
    isRunning   bool
    mu          sync.Mutex
}

type Task struct {
    ID          int
    Description string
    ProcessTime time.Duration
}

func NewTaskProcessor(workers int, queueSize int) *TaskProcessor {
    return &TaskProcessor{
        tasks:   make(chan Task, queueSize),
        workers: workers,
        stopCh:  make(chan struct{}),
    }
}

func (tp *TaskProcessor) Start() {
    tp.mu.Lock()
    defer tp.mu.Unlock()
    
    if tp.isRunning {
        return
    }
    
    tp.isRunning = true
    
    // Start worker goroutines
    for i := 1; i <= tp.workers; i++ {
        tp.wg.Add(1)
        go tp.worker(i)
    }
    
    fmt.Printf("Task processor started with %d workers\n", tp.workers)
}

func (tp *TaskProcessor) Stop() {
    tp.mu.Lock()
    defer tp.mu.Unlock()
    
    if !tp.isRunning {
        return
    }
    
    tp.isRunning = false
    close(tp.stopCh)
    tp.wg.Wait()
    
    fmt.Println("Task processor stopped")
}

func (tp *TaskProcessor) SubmitTask(task Task) bool {
    tp.mu.Lock()
    defer tp.mu.Unlock()
    
    if !tp.isRunning {
        return false
    }
    
    select {
    case tp.tasks <- task:
        fmt.Printf("Task %d submitted: %s\n", task.ID, task.Description)
        return true
    default:
        fmt.Printf("Task queue full, rejecting task %d\n", task.ID)
        return false
    }
}

func (tp *TaskProcessor) worker(id int) {
    defer tp.wg.Done()
    
    fmt.Printf("Worker %d started\n", id)
    
    for {
        select {
        case task := <-tp.tasks:
            fmt.Printf("Worker %d processing task %d: %s\n", id, task.ID, task.Description)
            time.Sleep(task.ProcessTime) // Simulate processing
            fmt.Printf("Worker %d completed task %d\n", id, task.ID)
            
        case <-tp.stopCh:
            fmt.Printf("Worker %d stopping\n", id)
            return
        }
    }
}

func backgroundTaskExample() {
    fmt.Println("\n=== Background Task Processing ===")
    
    // Create and start task processor
    processor := NewTaskProcessor(3, 10)
    processor.Start()
    
    // Submit tasks
    tasks := []Task{
        {ID: 1, Description: "Process user data", ProcessTime: 200 * time.Millisecond},
        {ID: 2, Description: "Send email notification", ProcessTime: 300 * time.Millisecond},
        {ID: 3, Description: "Generate report", ProcessTime: 500 * time.Millisecond},
        {ID: 4, Description: "Update database", ProcessTime: 100 * time.Millisecond},
        {ID: 5, Description: "Backup files", ProcessTime: 400 * time.Millisecond},
        {ID: 6, Description: "Clean cache", ProcessTime: 150 * time.Millisecond},
    }
    
    // Submit all tasks
    for _, task := range tasks {
        processor.SubmitTask(task)
        time.Sleep(50 * time.Millisecond) // Small delay between submissions
    }
    
    // Let tasks process for a while
    time.Sleep(1 * time.Second)
    
    // Graceful shutdown
    processor.Stop()
}
```

## Error Handling and Best Practices

### 1. **Goroutine Error Handling**
```go
// Error handling in goroutines
type WorkResult struct {
    ID     int
    Result string
    Error  error
}

func errorHandlingExample() {
    fmt.Println("\n=== Goroutine Error Handling ===")
    
    jobs := []int{1, 2, 3, 4, 5, 0, 7, 8} // 0 will cause division by zero
    results := make(chan WorkResult, len(jobs))
    
    var wg sync.WaitGroup
    
    // Process jobs concurrently
    for i, job := range jobs {
        wg.Add(1)
        go func(id, value int) {
            defer wg.Done()
            
            // Recover from panic
            defer func() {
                if r := recover(); r != nil {
                    results <- WorkResult{
                        ID:    id,
                        Error: fmt.Errorf("panic recovered: %v", r),
                    }
                }
            }()
            
            result, err := riskyWork(value)
            results <- WorkResult{
                ID:     id,
                Result: result,
                Error:  err,
            }
        }(i, job)
    }
    
    // Close results channel when all work is done
    go func() {
        wg.Wait()
        close(results)
    }()
    
    // Collect results
    var successCount, errorCount int
    for result := range results {
        if result.Error != nil {
            fmt.Printf("Job %d failed: %v\n", result.ID, result.Error)
            errorCount++
        } else {
            fmt.Printf("Job %d succeeded: %s\n", result.ID, result.Result)
            successCount++
        }
    }
    
    fmt.Printf("Summary: %d successful, %d failed\n", successCount, errorCount)
}

func riskyWork(value int) (string, error) {
    if value == 0 {
        return "", fmt.Errorf("cannot process zero value")
    }
    
    if value < 0 {
        panic("negative values not supported") // Will be recovered
    }
    
    // Simulate work
    time.Sleep(50 * time.Millisecond)
    
    return fmt.Sprintf("processed-%d", 100/value), nil
}
```

### 2. **Timeout and Context Usage**
```go
import "context"

func timeoutExample() {
    fmt.Println("\n=== Timeout and Context Example ===")
    
    // Context with timeout
    ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
    defer cancel()
    
    results := make(chan string, 3)
    
    // Start goroutines with different durations
    durations := []time.Duration{
        1 * time.Second,  // Will complete
        3 * time.Second,  // Will timeout
        500 * time.Millisecond, // Will complete
    }
    
    for i, duration := range durations {
        go func(id int, duration time.Duration) {
            select {
            case <-time.After(duration):
                results <- fmt.Sprintf("Task %d completed after %v", id, duration)
            case <-ctx.Done():
                results <- fmt.Sprintf("Task %d cancelled: %v", id, ctx.Err())
            }
        }(i, duration)
    }
    
    // Collect results
    for i := 0; i < len(durations); i++ {
        result := <-results
        fmt.Println(result)
    }
    
    // Context with cancellation
    fmt.Println("\n=== Context Cancellation Example ===")
    
    ctx2, cancel2 := context.WithCancel(context.Background())
    
    // Start a long-running goroutine
    go func() {
        ticker := time.NewTicker(200 * time.Millisecond)
        defer ticker.Stop()
        
        for {
            select {
            case <-ticker.C:
                fmt.Println("Long-running task working...")
            case <-ctx2.Done():
                fmt.Println("Long-running task cancelled")
                return
            }
        }
    }()
    
    // Let it run for a while
    time.Sleep(1 * time.Second)
    
    // Cancel the context
    cancel2()
    
    // Give it time to cleanup
    time.Sleep(300 * time.Millisecond)
}
```

## Main Function Orchestration

```go
func main() {
    fmt.Println("=== Goroutines Tutorial ===")
    
    // Basic examples
    basicGoroutineExamples()
    
    // Communication
    channelCommunication()
    
    // Synchronization
    waitGroupExample()
    
    // Advanced patterns
    workerPoolExample()
    producerConsumerExample()
    fanOutFanInExample()
    
    // Synchronization primitives
    mutexExample()
    rwMutexExample()
    
    // Real-world examples
    httpServerExample()
    backgroundTaskExample()
    
    // Error handling
    errorHandlingExample()
    timeoutExample()
    
    fmt.Printf("\nFinal goroutine count: %d\n", runtime.NumGoroutine())
    fmt.Println("Tutorial completed!")
}
```

## How to Run This

```powershell
cd 20_goroutines
go run .
```

## Key Takeaways

1. **Goroutines are lightweight threads** - can run thousands simultaneously
2. **Use channels for communication** - "Don't communicate by sharing memory; share memory by communicating"
3. **WaitGroup for synchronization** - wait for multiple goroutines to complete
4. **Mutex for shared state** - protect critical sections from race conditions
5. **Worker pools for controlled concurrency** - limit resource usage
6. **Context for cancellation and timeouts** - graceful shutdown and resource management
7. **Always handle goroutine errors** - use channels or other mechanisms to propagate errors
8. **Race conditions are real** - use proper synchronization primitives
9. **Don't leak goroutines** - ensure all goroutines can exit
10. **Profile concurrent code** - understand performance characteristics

Goroutines make concurrent programming accessible and powerful. They're the foundation of Go's ability to handle thousands of simultaneous operations efficiently, making Go perfect for web servers, microservices, and any application that needs to do multiple things at once!
