# Enums - Creating Named Constants and Enumerations

This folder teaches you about enumerations (enums) in Go - a way to define a set of named constants that represent distinct values. Go doesn't have a built-in enum keyword like some languages, but uses the `iota` identifier and constants to create powerful enumeration patterns.

## What This Teaches You

**Enum Fundamentals:**
- Using `iota` to create auto-incrementing constants
- Defining enum types with custom underlying types
- String representation of enums (Stringer interface)
- Enum validation and type safety
- Advanced iota patterns and bit flags
- JSON marshaling/unmarshaling with enums
- Best practices for enum design

## The Code Explained Simply

```go
package main
import "fmt"

// Define an enum type
type Weekday int

// Define enum values using iota
const (
    Sunday Weekday = iota  // 0
    Monday                 // 1 (iota increments automatically)
    Tuesday               // 2
    Wednesday             // 3  
    Thursday              // 4
    Friday                // 5
    Saturday              // 6
)

// Add methods to make enum more useful
func (w Weekday) String() string {
    switch w {
    case Sunday:
        return "Sunday"
    case Monday:
        return "Monday"
    case Tuesday:
        return "Tuesday"
    case Wednesday:
        return "Wednesday"
    case Thursday:
        return "Thursday"
    case Friday:
        return "Friday"
    case Saturday:
        return "Saturday"
    default:
        return "Unknown"
    }
}

func (w Weekday) IsWeekend() bool {
    return w == Sunday || w == Saturday
}

func main() {
    today := Tuesday
    fmt.Printf("Today is %s\n", today)           // Tuesday
    fmt.Printf("Is weekend? %t\n", today.IsWeekend()) // false
    
    weekend := Saturday
    fmt.Printf("%s is weekend? %t\n", weekend, weekend.IsWeekend()) // true
    
    // Enum values are just constants
    fmt.Printf("Monday value: %d\n", Monday)     // 1
    fmt.Printf("Friday value: %d\n", Friday)     // 5
}
```

## Basic Enum Patterns

### 1. **Simple Enumerations with iota**
```go
// Status enum
type Status int

const (
    StatusPending Status = iota  // 0
    StatusInProgress            // 1
    StatusCompleted             // 2
    StatusCancelled             // 3
)

// Priority enum
type Priority int

const (
    Low Priority = iota    // 0
    Medium                 // 1
    High                   // 2
    Critical              // 3
)

// Direction enum
type Direction int

const (
    North Direction = iota  // 0
    East                   // 1  
    South                  // 2
    West                   // 3
)

func (s Status) String() string {
    switch s {
    case StatusPending:
        return "Pending"
    case StatusInProgress:
        return "In Progress"
    case StatusCompleted:
        return "Completed"
    case StatusCancelled:
        return "Cancelled"
    default:
        return "Unknown Status"
    }
}

func (p Priority) String() string {
    names := []string{"Low", "Medium", "High", "Critical"}
    if int(p) >= 0 && int(p) < len(names) {
        return names[p]
    }
    return "Unknown Priority"
}

func (d Direction) String() string {
    directions := map[Direction]string{
        North: "North",
        East:  "East",
        South: "South",
        West:  "West",
    }
    if name, ok := directions[d]; ok {
        return name
    }
    return "Unknown Direction"
}

// Methods on enums
func (d Direction) Opposite() Direction {
    switch d {
    case North:
        return South
    case South:
        return North
    case East:
        return West
    case West:
        return East
    default:
        return d
    }
}

func (d Direction) TurnRight() Direction {
    switch d {
    case North:
        return East
    case East:
        return South
    case South:
        return West
    case West:
        return North
    default:
        return d
    }
}

func main() {
    // Task with status and priority
    task := struct {
        Name     string
        Status   Status
        Priority Priority
    }{
        Name:     "Implement user authentication",
        Status:   StatusInProgress,
        Priority: High,
    }
    
    fmt.Printf("Task: %s\n", task.Name)
    fmt.Printf("Status: %s (%d)\n", task.Status, task.Status)
    fmt.Printf("Priority: %s (%d)\n", task.Priority, task.Priority)
    
    // Direction navigation
    currentDir := North
    fmt.Printf("\nStarting direction: %s\n", currentDir)
    
    rightDir := currentDir.TurnRight()
    fmt.Printf("Turn right: %s\n", rightDir)
    
    oppositeDir := currentDir.Opposite()
    fmt.Printf("Opposite direction: %s\n", oppositeDir)
    
    // Enum comparison
    if task.Priority >= High {
        fmt.Println("This is a high-priority task!")
    }
    
    if task.Status == StatusInProgress {
        fmt.Println("Task is currently being worked on.")
    }
}
```

### 2. **Custom Starting Values and Increments**
```go
// HTTP Status codes
type HTTPStatus int

const (
    StatusOK                   HTTPStatus = 200
    StatusCreated             HTTPStatus = 201
    StatusAccepted            HTTPStatus = 202
    StatusNoContent           HTTPStatus = 204
    StatusBadRequest          HTTPStatus = 400
    StatusUnauthorized        HTTPStatus = 401
    StatusForbidden           HTTPStatus = 403
    StatusNotFound            HTTPStatus = 404
    StatusInternalServerError HTTPStatus = 500
)

// File permissions (powers of 2)
type Permission int

const (
    None    Permission = 0
    Read    Permission = 1 << iota  // 1 (2^0)
    Write                          // 2 (2^1)
    Execute                        // 4 (2^2)
)

// Combined permissions
const (
    ReadWrite Permission = Read | Write           // 3
    ReadExecute Permission = Read | Execute       // 5
    WriteExecute Permission = Write | Execute     // 6
    ReadWriteExecute Permission = Read | Write | Execute  // 7
)

// Size constants
type DataSize int64

const (
    Byte     DataSize = 1
    Kilobyte DataSize = 1024 * Byte
    Megabyte DataSize = 1024 * Kilobyte
    Gigabyte DataSize = 1024 * Megabyte
    Terabyte DataSize = 1024 * Gigabyte
)

func (hs HTTPStatus) String() string {
    statusNames := map[HTTPStatus]string{
        StatusOK:                   "OK",
        StatusCreated:             "Created",
        StatusAccepted:            "Accepted", 
        StatusNoContent:           "No Content",
        StatusBadRequest:          "Bad Request",
        StatusUnauthorized:        "Unauthorized",
        StatusForbidden:           "Forbidden",
        StatusNotFound:            "Not Found",
        StatusInternalServerError: "Internal Server Error",
    }
    
    if name, ok := statusNames[hs]; ok {
        return fmt.Sprintf("%d %s", int(hs), name)
    }
    return fmt.Sprintf("%d Unknown", int(hs))
}

func (hs HTTPStatus) IsSuccess() bool {
    return hs >= 200 && hs < 300
}

func (hs HTTPStatus) IsClientError() bool {
    return hs >= 400 && hs < 500
}

func (hs HTTPStatus) IsServerError() bool {
    return hs >= 500 && hs < 600
}

func (p Permission) String() string {
    if p == None {
        return "None"
    }
    
    var parts []string
    if p&Read != 0 {
        parts = append(parts, "Read")
    }
    if p&Write != 0 {
        parts = append(parts, "Write")
    }
    if p&Execute != 0 {
        parts = append(parts, "Execute")
    }
    
    return strings.Join(parts, "+")
}

func (p Permission) HasRead() bool {
    return p&Read != 0
}

func (p Permission) HasWrite() bool {
    return p&Write != 0
}

func (p Permission) HasExecute() bool {
    return p&Execute != 0
}

func (ds DataSize) String() string {
    switch {
    case ds >= Terabyte:
        return fmt.Sprintf("%.2f TB", float64(ds)/float64(Terabyte))
    case ds >= Gigabyte:
        return fmt.Sprintf("%.2f GB", float64(ds)/float64(Gigabyte))
    case ds >= Megabyte:
        return fmt.Sprintf("%.2f MB", float64(ds)/float64(Megabyte))
    case ds >= Kilobyte:
        return fmt.Sprintf("%.2f KB", float64(ds)/float64(Kilobyte))
    default:
        return fmt.Sprintf("%d bytes", ds)
    }
}

func main() {
    // HTTP Status examples
    responses := []HTTPStatus{
        StatusOK,
        StatusCreated,
        StatusBadRequest,
        StatusNotFound,
        StatusInternalServerError,
    }
    
    fmt.Println("HTTP Response Status:")
    for _, status := range responses {
        fmt.Printf("  %s - Success: %t, Client Error: %t, Server Error: %t\n",
            status, status.IsSuccess(), status.IsClientError(), status.IsServerError())
    }
    
    // Permission examples
    permissions := []Permission{
        None,
        Read,
        Write,
        Execute,
        ReadWrite,
        ReadExecute,
        ReadWriteExecute,
    }
    
    fmt.Println("\nFile Permissions:")
    for _, perm := range permissions {
        fmt.Printf("  %s (value: %d) - Read: %t, Write: %t, Execute: %t\n",
            perm, int(perm), perm.HasRead(), perm.HasWrite(), perm.HasExecute())
    }
    
    // Data size examples
    sizes := []DataSize{
        500 * Byte,
        2 * Kilobyte,
        15 * Megabyte,
        3 * Gigabyte,
        DataSize(1.5 * float64(Terabyte)),
    }
    
    fmt.Println("\nData Sizes:")
    for _, size := range sizes {
        fmt.Printf("  %s\n", size)
    }
}
```

### 3. **Advanced iota Patterns**
```go
// Skip values with blank identifier
type Color int

const (
    Red Color = iota    // 0
    Green              // 1
    Blue               // 2
    _                  // 3 (skipped)
    Yellow             // 4
    Orange             // 5
)

// Complex calculations with iota
type TimeUnit int64

const (
    Nanosecond  TimeUnit = 1
    Microsecond          = 1000 * Nanosecond
    Millisecond          = 1000 * Microsecond  
    Second               = 1000 * Millisecond
    Minute               = 60 * Second
    Hour                 = 60 * Minute
    Day                  = 24 * Hour
)

// Binary flags with iota
type Feature int

const (
    FeatureLogging Feature = 1 << iota  // 1
    FeatureCaching                      // 2  
    FeatureMetrics                      // 4
    FeatureTracing                      // 8
    FeatureAuth                         // 16
    FeatureSSL                          // 32
)

// Network protocols with custom values
type Protocol int

const (
    HTTP Protocol = 80
    HTTPS Protocol = 443
    FTP Protocol = 21
    SSH Protocol = 22
    SMTP Protocol = 25
    DNS Protocol = 53
)

// Enum with string underlying type
type Environment string

const (
    Development Environment = "development"
    Staging     Environment = "staging"
    Production  Environment = "production"
)

func (c Color) String() string {
    colors := []string{"Red", "Green", "Blue", "", "Yellow", "Orange"}
    if int(c) >= 0 && int(c) < len(colors) && colors[c] != "" {
        return colors[c]
    }
    return "Unknown Color"
}

func (tu TimeUnit) String() string {
    switch {
    case tu >= Day:
        return fmt.Sprintf("%.1f days", float64(tu)/float64(Day))
    case tu >= Hour:
        return fmt.Sprintf("%.1f hours", float64(tu)/float64(Hour))
    case tu >= Minute:
        return fmt.Sprintf("%.1f minutes", float64(tu)/float64(Minute))
    case tu >= Second:
        return fmt.Sprintf("%.1f seconds", float64(tu)/float64(Second))
    case tu >= Millisecond:
        return fmt.Sprintf("%.1f ms", float64(tu)/float64(Millisecond))
    case tu >= Microsecond:
        return fmt.Sprintf("%.1f μs", float64(tu)/float64(Microsecond))
    default:
        return fmt.Sprintf("%d ns", tu)
    }
}

func (f Feature) String() string {
    if f == 0 {
        return "No Features"
    }
    
    features := []string{}
    featureNames := map[Feature]string{
        FeatureLogging: "Logging",
        FeatureCaching: "Caching", 
        FeatureMetrics: "Metrics",
        FeatureTracing: "Tracing",
        FeatureAuth:    "Auth",
        FeatureSSL:     "SSL",
    }
    
    for feature, name := range featureNames {
        if f&feature != 0 {
            features = append(features, name)
        }
    }
    
    return strings.Join(features, " + ")
}

func (f Feature) Has(feature Feature) bool {
    return f&feature != 0
}

func (f Feature) Add(feature Feature) Feature {
    return f | feature
}

func (f Feature) Remove(feature Feature) Feature {
    return f &^ feature
}

func (p Protocol) String() string {
    protocols := map[Protocol]string{
        HTTP:  "HTTP",
        HTTPS: "HTTPS", 
        FTP:   "FTP",
        SSH:   "SSH",
        SMTP:  "SMTP",
        DNS:   "DNS",
    }
    
    if name, ok := protocols[p]; ok {
        return fmt.Sprintf("%s:%d", name, int(p))
    }
    return fmt.Sprintf("Unknown:%d", int(p))
}

func (e Environment) IsProduction() bool {
    return e == Production
}

func (e Environment) String() string {
    return string(e)
}

func main() {
    // Color enum with gaps
    fmt.Println("Colors:")
    colors := []Color{Red, Green, Blue, Yellow, Orange}
    for _, color := range colors {
        fmt.Printf("  %s (value: %d)\n", color, int(color))
    }
    
    // Time units
    fmt.Println("\nTime Units:")
    durations := []TimeUnit{
        500 * Nanosecond,
        750 * Microsecond,
        250 * Millisecond,
        30 * Second,
        45 * Minute,
        8 * Hour,
        3 * Day,
    }
    
    for _, duration := range durations {
        fmt.Printf("  %s\n", duration)
    }
    
    // Feature flags
    fmt.Println("\nFeature Flags:")
    config := FeatureLogging | FeatureMetrics | FeatureAuth
    fmt.Printf("Current config: %s\n", config)
    fmt.Printf("Has logging: %t\n", config.Has(FeatureLogging))
    fmt.Printf("Has caching: %t\n", config.Has(FeatureCaching))
    
    // Add and remove features
    config = config.Add(FeatureCaching)
    fmt.Printf("After adding caching: %s\n", config)
    
    config = config.Remove(FeatureAuth)
    fmt.Printf("After removing auth: %s\n", config)
    
    // Protocol enum
    fmt.Println("\nProtocols:")
    protocols := []Protocol{HTTP, HTTPS, FTP, SSH}
    for _, protocol := range protocols {
        fmt.Printf("  %s\n", protocol)
    }
    
    // Environment enum
    fmt.Println("\nEnvironments:")
    environments := []Environment{Development, Staging, Production}
    for _, env := range environments {
        fmt.Printf("  %s (Production: %t)\n", env, env.IsProduction())
    }
}
```

## JSON Marshaling with Enums

### 1. **Custom JSON Representation**
```go
import (
    "encoding/json"
    "fmt"
    "strings"
)

type OrderStatus int

const (
    OrderStatusPending OrderStatus = iota
    OrderStatusProcessing
    OrderStatusShipped
    OrderStatusDelivered
    OrderStatusCancelled
)

func (os OrderStatus) String() string {
    switch os {
    case OrderStatusPending:
        return "pending"
    case OrderStatusProcessing:
        return "processing"
    case OrderStatusShipped:
        return "shipped"
    case OrderStatusDelivered:
        return "delivered"
    case OrderStatusCancelled:
        return "cancelled"
    default:
        return "unknown"
    }
}

// Custom JSON marshaling
func (os OrderStatus) MarshalJSON() ([]byte, error) {
    return json.Marshal(os.String())
}

// Custom JSON unmarshaling
func (os *OrderStatus) UnmarshalJSON(data []byte) error {
    var statusStr string
    if err := json.Unmarshal(data, &statusStr); err != nil {
        return err
    }
    
    switch strings.ToLower(statusStr) {
    case "pending":
        *os = OrderStatusPending
    case "processing":
        *os = OrderStatusProcessing
    case "shipped":
        *os = OrderStatusShipped
    case "delivered":
        *os = OrderStatusDelivered
    case "cancelled":
        *os = OrderStatusCancelled
    default:
        return fmt.Errorf("unknown order status: %s", statusStr)
    }
    
    return nil
}

type UserRole string

const (
    RoleGuest  UserRole = "guest"
    RoleUser   UserRole = "user" 
    RoleAdmin  UserRole = "admin"
    RoleOwner  UserRole = "owner"
)

func (ur UserRole) CanEdit() bool {
    return ur == RoleAdmin || ur == RoleOwner
}

func (ur UserRole) CanDelete() bool {
    return ur == RoleOwner
}

// Order struct using enums
type Order struct {
    ID       int         `json:"id"`
    Status   OrderStatus `json:"status"`
    Items    []string    `json:"items"`
    Total    float64     `json:"total"`
}

type User struct {
    ID       int      `json:"id"`
    Username string   `json:"username"`
    Email    string   `json:"email"`
    Role     UserRole `json:"role"`
}

func main() {
    // Create orders with different statuses
    orders := []Order{
        {ID: 1, Status: OrderStatusPending, Items: []string{"laptop", "mouse"}, Total: 1299.99},
        {ID: 2, Status: OrderStatusShipped, Items: []string{"book"}, Total: 29.99},
        {ID: 3, Status: OrderStatusDelivered, Items: []string{"headphones"}, Total: 199.99},
    }
    
    // Marshal to JSON
    ordersJSON, err := json.MarshalIndent(orders, "", "  ")
    if err != nil {
        fmt.Printf("JSON marshal error: %v\n", err)
        return
    }
    
    fmt.Println("Orders as JSON:")
    fmt.Println(string(ordersJSON))
    
    // Unmarshal from JSON
    jsonData := `[
        {
            "id": 4,
            "status": "processing",
            "items": ["tablet", "case"],
            "total": 899.99
        },
        {
            "id": 5,
            "status": "cancelled",
            "items": ["phone"],
            "total": 699.99
        }
    ]`
    
    var newOrders []Order
    err = json.Unmarshal([]byte(jsonData), &newOrders)
    if err != nil {
        fmt.Printf("JSON unmarshal error: %v\n", err)
        return
    }
    
    fmt.Println("\nParsed orders:")
    for _, order := range newOrders {
        fmt.Printf("Order %d: %s - $%.2f\n", order.ID, order.Status, order.Total)
    }
    
    // User roles example
    users := []User{
        {ID: 1, Username: "alice", Email: "alice@example.com", Role: RoleAdmin},
        {ID: 2, Username: "bob", Email: "bob@example.com", Role: RoleUser},
        {ID: 3, Username: "charlie", Email: "charlie@example.com", Role: RoleOwner},
    }
    
    fmt.Println("\nUser permissions:")
    for _, user := range users {
        fmt.Printf("User %s (%s) - Can edit: %t, Can delete: %t\n",
            user.Username, user.Role, user.Role.CanEdit(), user.Role.CanDelete())
    }
}
```

### 2. **Validation and Type Safety**
```go
// Day of week enum with validation
type DayOfWeek int

const (
    Sunday DayOfWeek = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
)

// Valid checks if the enum value is valid
func (d DayOfWeek) Valid() bool {
    return d >= Sunday && d <= Saturday
}

func (d DayOfWeek) String() string {
    if !d.Valid() {
        return "Invalid Day"
    }
    
    days := []string{
        "Sunday", "Monday", "Tuesday", "Wednesday",
        "Thursday", "Friday", "Saturday",
    }
    return days[d]
}

func (d DayOfWeek) IsWeekend() bool {
    return d.Valid() && (d == Sunday || d == Saturday)
}

// Parse day from string
func ParseDayOfWeek(s string) (DayOfWeek, error) {
    days := map[string]DayOfWeek{
        "sunday":    Sunday,
        "monday":    Monday,
        "tuesday":   Tuesday,
        "wednesday": Wednesday,
        "thursday":  Thursday,
        "friday":    Friday,
        "saturday":  Saturday,
    }
    
    day, ok := days[strings.ToLower(s)]
    if !ok {
        return 0, fmt.Errorf("invalid day of week: %s", s)
    }
    
    return day, nil
}

// All returns all valid enum values
func AllDaysOfWeek() []DayOfWeek {
    return []DayOfWeek{Sunday, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday}
}

// Grade enum with validation
type Grade int

const (
    GradeA Grade = iota + 1  // Start from 1
    GradeB
    GradeC
    GradeD
    GradeF
)

func (g Grade) Valid() bool {
    return g >= GradeA && g <= GradeF
}

func (g Grade) String() string {
    if !g.Valid() {
        return "Invalid Grade"
    }
    
    grades := map[Grade]string{
        GradeA: "A",
        GradeB: "B", 
        GradeC: "C",
        GradeD: "D",
        GradeF: "F",
    }
    return grades[g]
}

func (g Grade) IsPassing() bool {
    return g.Valid() && g < GradeF
}

func (g Grade) GPA() float64 {
    if !g.Valid() {
        return 0.0
    }
    
    gpaMap := map[Grade]float64{
        GradeA: 4.0,
        GradeB: 3.0,
        GradeC: 2.0,
        GradeD: 1.0,
        GradeF: 0.0,
    }
    return gpaMap[g]
}

// Student record
type StudentRecord struct {
    StudentID int
    Course    string
    Grade     Grade
}

func (sr StudentRecord) IsValid() bool {
    return sr.StudentID > 0 && sr.Course != "" && sr.Grade.Valid()
}

func validateGrades(records []StudentRecord) []error {
    var errors []error
    
    for i, record := range records {
        if !record.IsValid() {
            errors = append(errors, fmt.Errorf("record %d is invalid: StudentID=%d, Course=%s, Grade=%s", 
                i, record.StudentID, record.Course, record.Grade))
        }
    }
    
    return errors
}

func main() {
    // Day of week validation
    fmt.Println("Day of Week Examples:")
    
    dayStrings := []string{"monday", "friday", "saturday", "invalid", "TUESDAY"}
    for _, dayStr := range dayStrings {
        day, err := ParseDayOfWeek(dayStr)
        if err != nil {
            fmt.Printf("  Error parsing '%s': %v\n", dayStr, err)
        } else {
            fmt.Printf("  '%s' -> %s (Weekend: %t)\n", dayStr, day, day.IsWeekend())
        }
    }
    
    // All days
    fmt.Println("\nAll days of week:")
    for _, day := range AllDaysOfWeek() {
        fmt.Printf("  %s (Weekend: %t)\n", day, day.IsWeekend())
    }
    
    // Grade validation
    fmt.Println("\nGrade Examples:")
    
    // Valid grades
    validGrades := []Grade{GradeA, GradeB, GradeC, GradeD, GradeF}
    for _, grade := range validGrades {
        fmt.Printf("  Grade %s: GPA %.1f, Passing: %t\n", 
            grade, grade.GPA(), grade.IsPassing())
    }
    
    // Invalid grade
    invalidGrade := Grade(99)
    fmt.Printf("  Invalid grade (99): %s, Valid: %t\n", invalidGrade, invalidGrade.Valid())
    
    // Student records
    fmt.Println("\nStudent Records:")
    records := []StudentRecord{
        {StudentID: 1, Course: "Math", Grade: GradeA},
        {StudentID: 2, Course: "Physics", Grade: GradeB},
        {StudentID: 0, Course: "Chemistry", Grade: GradeC},  // Invalid: StudentID = 0
        {StudentID: 3, Course: "", Grade: GradeD},           // Invalid: empty course
        {StudentID: 4, Course: "History", Grade: Grade(99)}, // Invalid: invalid grade
        {StudentID: 5, Course: "English", Grade: GradeF},
    }
    
    errors := validateGrades(records)
    if len(errors) > 0 {
        fmt.Println("Validation errors:")
        for _, err := range errors {
            fmt.Printf("  %v\n", err)
        }
    }
    
    fmt.Println("\nValid records:")
    for i, record := range records {
        if record.IsValid() {
            fmt.Printf("  Record %d: Student %d got %s in %s (GPA: %.1f)\n",
                i, record.StudentID, record.Grade, record.Course, record.Grade.GPA())
        }
    }
}
```

## State Machine with Enums

### 1. **Traffic Light State Machine**
```go
type TrafficLightState int

const (
    Red TrafficLightState = iota
    Yellow
    Green
)

func (tls TrafficLightState) String() string {
    switch tls {
    case Red:
        return "Red"
    case Yellow:
        return "Yellow"
    case Green:
        return "Green"
    default:
        return "Unknown"
    }
}

func (tls TrafficLightState) Next() TrafficLightState {
    switch tls {
    case Red:
        return Green
    case Yellow:
        return Red
    case Green:
        return Yellow
    default:
        return Red
    }
}

func (tls TrafficLightState) CanGo() bool {
    return tls == Green
}

func (tls TrafficLightState) ShouldStop() bool {
    return tls == Red
}

func (tls TrafficLightState) ShouldPrepareToStop() bool {
    return tls == Yellow
}

// Traffic light controller
type TrafficLight struct {
    CurrentState TrafficLightState
    Location     string
}

func NewTrafficLight(location string) *TrafficLight {
    return &TrafficLight{
        CurrentState: Red,  // Start with red for safety
        Location:     location,
    }
}

func (tl *TrafficLight) Cycle() {
    fmt.Printf("Traffic light at %s: %s -> ", tl.Location, tl.CurrentState)
    tl.CurrentState = tl.CurrentState.Next()
    fmt.Printf("%s\n", tl.CurrentState)
}

func (tl *TrafficLight) Status() string {
    return fmt.Sprintf("Traffic light at %s is %s", tl.Location, tl.CurrentState)
}

func (tl *TrafficLight) CanVehiclesProceed() bool {
    return tl.CurrentState.CanGo()
}

// Connection state enum for network connections
type ConnectionState int

const (
    Disconnected ConnectionState = iota
    Connecting
    Connected
    Disconnecting
    Failed
)

func (cs ConnectionState) String() string {
    states := []string{
        "Disconnected", "Connecting", "Connected", "Disconnecting", "Failed",
    }
    if int(cs) >= 0 && int(cs) < len(states) {
        return states[cs]
    }
    return "Unknown"
}

// Valid state transitions
func (cs ConnectionState) CanTransitionTo(newState ConnectionState) bool {
    switch cs {
    case Disconnected:
        return newState == Connecting
    case Connecting:
        return newState == Connected || newState == Failed || newState == Disconnected
    case Connected:
        return newState == Disconnecting || newState == Failed
    case Disconnecting:
        return newState == Disconnected
    case Failed:
        return newState == Disconnected || newState == Connecting
    default:
        return false
    }
}

// Network connection
type Connection struct {
    ID       string
    State    ConnectionState
    Host     string
    Port     int
    LastError error
}

func NewConnection(id, host string, port int) *Connection {
    return &Connection{
        ID:    id,
        State: Disconnected,
        Host:  host,
        Port:  port,
    }
}

func (c *Connection) Connect() error {
    if !c.State.CanTransitionTo(Connecting) {
        return fmt.Errorf("cannot connect from state %s", c.State)
    }
    
    fmt.Printf("Connection %s: %s -> Connecting\n", c.ID, c.State)
    c.State = Connecting
    
    // Simulate connection process
    fmt.Printf("Connecting to %s:%d...\n", c.Host, c.Port)
    
    // Simulate success (in real code, this would be actual network logic)
    if c.Host == "unreachable.com" {
        c.State = Failed
        c.LastError = fmt.Errorf("connection failed: host unreachable")
        fmt.Printf("Connection %s: Failed\n", c.ID)
        return c.LastError
    }
    
    c.State = Connected
    c.LastError = nil
    fmt.Printf("Connection %s: Connected\n", c.ID)
    return nil
}

func (c *Connection) Disconnect() error {
    if !c.State.CanTransitionTo(Disconnecting) {
        return fmt.Errorf("cannot disconnect from state %s", c.State)
    }
    
    fmt.Printf("Connection %s: %s -> Disconnecting\n", c.ID, c.State)
    c.State = Disconnecting
    
    // Simulate disconnection
    fmt.Printf("Disconnecting from %s:%d...\n", c.Host, c.Port)
    
    c.State = Disconnected
    fmt.Printf("Connection %s: Disconnected\n", c.ID)
    return nil
}

func (c *Connection) Status() string {
    status := fmt.Sprintf("Connection %s (%s:%d): %s", c.ID, c.Host, c.Port, c.State)
    if c.LastError != nil {
        status += fmt.Sprintf(" - Error: %v", c.LastError)
    }
    return status
}

func main() {
    // Traffic light simulation
    fmt.Println("Traffic Light Simulation:")
    light := NewTrafficLight("Main St & Oak Ave")
    
    fmt.Println(light.Status())
    
    // Cycle through states
    for i := 0; i < 6; i++ {
        light.Cycle()
        fmt.Printf("Can vehicles proceed? %t\n", light.CanVehiclesProceed())
        fmt.Println()
    }
    
    fmt.Println("=====================================")
    
    // Connection state machine
    fmt.Println("Connection State Machine:")
    
    connections := []*Connection{
        NewConnection("conn1", "example.com", 80),
        NewConnection("conn2", "unreachable.com", 443),
    }
    
    for _, conn := range connections {
        fmt.Println(conn.Status())
        
        // Try to connect
        err := conn.Connect()
        if err != nil {
            fmt.Printf("Connection error: %v\n", err)
        }
        
        fmt.Println(conn.Status())
        
        // Try to disconnect if connected
        if conn.State == Connected {
            err = conn.Disconnect()
            if err != nil {
                fmt.Printf("Disconnection error: %v\n", err)
            }
        }
        
        fmt.Println(conn.Status())
        fmt.Println()
    }
}
```

## Enum Best Practices

### 1. **Comprehensive Enum Design**
```go
// Well-designed enum with all necessary features
type LogLevel int

const (
    LogLevelTrace LogLevel = iota
    LogLevelDebug
    LogLevelInfo
    LogLevelWarn
    LogLevelError
    LogLevelFatal
)

// String representation
func (ll LogLevel) String() string {
    switch ll {
    case LogLevelTrace:
        return "TRACE"
    case LogLevelDebug:
        return "DEBUG"
    case LogLevelInfo:
        return "INFO"
    case LogLevelWarn:
        return "WARN"
    case LogLevelError:
        return "ERROR"
    case LogLevelFatal:
        return "FATAL"
    default:
        return "UNKNOWN"
    }
}

// Validation
func (ll LogLevel) Valid() bool {
    return ll >= LogLevelTrace && ll <= LogLevelFatal
}

// Comparison methods
func (ll LogLevel) IsAtLeast(other LogLevel) bool {
    return ll >= other
}

func (ll LogLevel) ShouldLog(configLevel LogLevel) bool {
    return ll >= configLevel
}

// Color representation (for terminal output)
func (ll LogLevel) Color() string {
    switch ll {
    case LogLevelTrace:
        return "\033[37m"  // White
    case LogLevelDebug:
        return "\033[36m"  // Cyan
    case LogLevelInfo:
        return "\033[32m"  // Green
    case LogLevelWarn:
        return "\033[33m"  // Yellow
    case LogLevelError:
        return "\033[31m"  // Red
    case LogLevelFatal:
        return "\033[35m"  // Magenta
    default:
        return "\033[0m"   // Reset
    }
}

// Parse from string
func ParseLogLevel(s string) (LogLevel, error) {
    switch strings.ToUpper(strings.TrimSpace(s)) {
    case "TRACE":
        return LogLevelTrace, nil
    case "DEBUG":
        return LogLevelDebug, nil
    case "INFO":
        return LogLevelInfo, nil
    case "WARN", "WARNING":
        return LogLevelWarn, nil
    case "ERROR":
        return LogLevelError, nil
    case "FATAL":
        return LogLevelFatal, nil
    default:
        return 0, fmt.Errorf("invalid log level: %s", s)
    }
}

// Get all valid values
func AllLogLevels() []LogLevel {
    return []LogLevel{
        LogLevelTrace, LogLevelDebug, LogLevelInfo,
        LogLevelWarn, LogLevelError, LogLevelFatal,
    }
}

// JSON marshaling
func (ll LogLevel) MarshalJSON() ([]byte, error) {
    return json.Marshal(ll.String())
}

func (ll *LogLevel) UnmarshalJSON(data []byte) error {
    var s string
    if err := json.Unmarshal(data, &s); err != nil {
        return err
    }
    
    level, err := ParseLogLevel(s)
    if err != nil {
        return err
    }
    
    *ll = level
    return nil
}

// Logger using the enum
type Logger struct {
    MinLevel LogLevel
    Name     string
}

func NewLogger(name string, minLevel LogLevel) *Logger {
    return &Logger{
        Name:     name,
        MinLevel: minLevel,
    }
}

func (l *Logger) log(level LogLevel, message string) {
    if !level.ShouldLog(l.MinLevel) {
        return
    }
    
    timestamp := time.Now().Format("2006-01-02 15:04:05")
    colorCode := level.Color()
    resetCode := "\033[0m"
    
    fmt.Printf("%s[%s] %s%s%s [%s]: %s\n",
        colorCode, timestamp, level, resetCode, colorCode, l.Name, message)
    fmt.Print(resetCode)
}

func (l *Logger) Trace(message string) { l.log(LogLevelTrace, message) }
func (l *Logger) Debug(message string) { l.log(LogLevelDebug, message) }
func (l *Logger) Info(message string)  { l.log(LogLevelInfo, message) }
func (l *Logger) Warn(message string)  { l.log(LogLevelWarn, message) }
func (l *Logger) Error(message string) { l.log(LogLevelError, message) }
func (l *Logger) Fatal(message string) { l.log(LogLevelFatal, message) }

func main() {
    // Test log level parsing
    fmt.Println("Log Level Parsing:")
    testLevels := []string{"trace", "DEBUG", "info", "WARN", "error", "fatal", "invalid"}
    
    for _, levelStr := range testLevels {
        level, err := ParseLogLevel(levelStr)
        if err != nil {
            fmt.Printf("  '%s' -> Error: %v\n", levelStr, err)
        } else {
            fmt.Printf("  '%s' -> %s (value: %d)\n", levelStr, level, int(level))
        }
    }
    
    fmt.Println("\nAll Log Levels:")
    for _, level := range AllLogLevels() {
        fmt.Printf("  %s (value: %d, valid: %t)\n", level, int(level), level.Valid())
    }
    
    fmt.Println("\nLogger Demo:")
    
    // Create logger with INFO level
    logger := NewLogger("MyApp", LogLevelInfo)
    
    // Test all log levels
    logger.Trace("This is a trace message")  // Won't show (below INFO)
    logger.Debug("This is a debug message")  // Won't show (below INFO)
    logger.Info("Application started")       // Will show
    logger.Warn("This is a warning")         // Will show
    logger.Error("An error occurred")        // Will show
    logger.Fatal("Fatal error!")             // Will show
    
    fmt.Println("\nLevel Comparisons:")
    currentLevel := LogLevelWarn
    testLevels2 := AllLogLevels()
    
    for _, level := range testLevels2 {
        fmt.Printf("  %s >= %s: %t (should log: %t)\n",
            level, currentLevel, level.IsAtLeast(currentLevel), level.ShouldLog(currentLevel))
    }
}
```

## How to Run This

```powershell
cd 18_enums
go run .
```

## Key Takeaways

1. **Use `iota` for auto-incrementing constants** - simplifies enum definition
2. **Create methods on enum types** - add behavior like String(), Valid(), etc.
3. **Implement Stringer interface** - provides meaningful string representation
4. **Validate enum values** - check if enum value is within valid range
5. **Use typed constants** - prevents mixing different enum types
6. **Support JSON marshaling** - custom MarshalJSON/UnmarshalJSON for APIs
7. **Bit flags with powers of 2** - use `1 << iota` for combinable flags
8. **State machines with enums** - model state transitions and validation

Enums are like having a controlled vocabulary for your program - instead of using magic numbers or arbitrary strings, you define a specific set of meaningful values that represent different states, options, or categories. This makes your code more readable, type-safe, and less prone to errors!
