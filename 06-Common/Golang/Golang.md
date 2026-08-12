
# Go Language – Full Notes

java complex but fast  
python easy but slow interpreter  
C slow for compilation time  

To overcome this we use **Go**, developed by Google in 2007.

## Features

- Inbuilt support for garbage collection
- Strong and statically typed
- Simplicity
- FAST compile time

## Where to Use

- Web development (server-side)
- Developing network-based programs
- Developing cross-platform enterprise applications
- Cloud-native development

## Why Use Go

- Good memory management
- Works cross platform
- Fast runtime and compile time

## Structure

- Package declaration
- Import package
- Functions
- Variables
- Statements and expressions
- Comments

**`.go` extension example:** `hello.go`

---

## Basic Program

**Declaration package:** `package main`  
**Package import:** `import ("fmt")`  
**Function declaration:**


func main() {
    fmt.Println("hello world")
}


---

## Commands

- Block comment: `/* */`
- Line comment: `//`
- Run: `go run filename.go`
- Check version: `go version`
- Build `.exe` (Windows distribution): `go build filename.go`

---

## Variable Declaration

- Long form: `var variablename type = value`
- Short form: `variablename := value`

**Multiple variable examples:**
- `var a, b, c, d int = 1, 3, 5, 7`
- `var a, b = 6, "Hello"`
- `c, d := 7, "World!"`

### `var` vs `:=`

**`var`**
- Can be used inside and outside of functions
- Declaration and assignment can be done separately

**`:=`**
- Can only be used inside functions
- Declaration and assignment must be in the same line

---

## Constants

`const` declares a variable as constant (unchangeable, read-only).


const PI = 3.14


Typed and untyped constants exist.

---

## Output Functions

- `print()`
- `printf("...")`
- `println()`

### Common Format Verbs

| Verb | Description | Example | Output |
|------|-------------|---------|--------|
| `%v` | Value default format | `fmt.Printf("%v\n", txt)` | Hello World! |
| `%#v` | Go-syntax format | `fmt.Printf("%#v\n", txt)` | "Hello World!" |
| `%T` | Type of value | `fmt.Printf("%T\n", txt)` | string |
| `%%` | Percent sign | | |
| `%b` | Base 2 | `fmt.Printf("%b\n", i)` | 1111 |
| `%d` | Base 10 | `fmt.Printf("%d\n", i)` | 15 |
| `%+d` | Base 10 with sign | `fmt.Printf("%+d\n", i)` | +15 |
| `%o` | Base 8 | `fmt.Printf("%o\n", i)` | 17 |
| `%O` | Base 8 with 0o | `fmt.Printf("%O\n", i)` | 0o17 |
| `%x` | Base 16 lowercase | `fmt.Printf("%x\n", i)` | f |
| `%X` | Base 16 uppercase | `fmt.Printf("%X\n", i)` | F |
| `%#x` | Base 16 with 0x | `fmt.Printf("%#x\n", i)` | 0xf |
| `%4d` | Width 4 right-justified | `fmt.Printf("%4d\n", i)` | 15 |
| `%-4d` | Width 4 left-justified | `fmt.Printf("%-4d\n", i)` | 15 |
| `%04d` | Pad with zeroes | `fmt.Printf("%04d\n", i)` | 0015 |
| `%s` | String | `fmt.Printf("%s\n", txt)` | Hello |
| `%q` | Double-quoted string | `fmt.Printf("%q\n", txt)` | "Hello" |
| `%8s` | Width 8 right-justified | `fmt.Printf("%8s\n", txt)` | Hello |
| `%-8s` | Width 8 left-justified | `fmt.Printf("%-8s\n", txt)` | Hello |
| `%x` | Hex dump of bytes | `fmt.Printf("%x\n", txt)` | 48656c6c6f |
| `% x` | Hex dump with spaces | `fmt.Printf("% x\n", txt)` | 48 65 6c 6c 6f |

---

## Input


fmt.Scanln(&a)
fmt.Println(a)


---

## Default Values

- `int` → `0`
- `string` → `""`
- `bool` → `false`

---

## Data Types

- Integer: `int`, `uint` with sizes 8,16,32,64 (signed, unsigned)
- Float: `float32`, `float64`
- `string`
- `bool`
- Complex: `complex64`, `complex128`

**Example:**


ru := 'a'
fmt.Printf("%v %T", ru, ru) // 97 int32 (ASCII)


---

## Arrays

With `var`:


var arr1 = int{1, 2, 3}[11]
var arr2 = [...]int{1, 2, 3}


With `:=`:


arr := int{4, 5, 6, 7, 8}[12]
arr2 := [...]int{4, 5, 6, 7, 8}


Arrays have fixed length.

---

## Slices

More powerful and dynamic than arrays.


myslice := []int{1, 2, 3}
len(myslice) // length
cap(myslice) // capacity


**Create from array:**


var arr1 = []int{1, 2, 3}
myslice := arr1[2:4]


**Using `make`:**


myslice1 := make([]int, 5, 10)


**Append:**


myslice1 = append(myslice1, 20, 21)
myslice3 := append(myslice1, myslice2...)


**Copy:**


n := copy(dest, src)


---

## Operators

### Arithmetic

- `+` addition
- `-` subtraction
- `*` multiplication
- `/` division
- `%` modulus
- `++` increment
- `--` decrement

### Assignment

| Operator | Example | Same As |
|----------|---------|---------|
| `=` | `x = 5` | `x = 5` |
| `+=` | `x += 3` | `x = x + 3` |
| `-=` | `x -= 3` | `x = x - 3` |
| `*=` | `x *= 3` | `x = x * 3` |
| `/=` | `x /= 3` | `x = x / 3` |
| `%=` | `x %= 3` | `x = x % 3` |
| `&=` | `x &= 3` | `x = x & 3` |
| `|=` | `x |= 3` | `x = x | 3` |
| `^=` | `x ^= 3` | `x = x ^ 3` |
| `>>=` | `x >>= 3` | `x = x >> 3` |
| `<<=` | `x <<= 3` | `x = x << 3` |

### Comparison

- `==` equal
- `!=` not equal
- `>` greater than
- `<` less than
- `>=` greater or equal
- `<=` less or equal

### Logical & Bitwise

| Operator | Name | Description | Example |
|----------|------|-------------|---------|
| `&&` | Logical and | Returns true if both are true | `x < 5 && x < 10` |
| `||` | Logical or | Returns true if one is true | `x < 5 || x < 4` |
| `!` | Logical not | Reverses the result | `!(x < 5 && x < 10)` |
| `&` | Bitwise AND | Sets bit to 1 if both bits are 1 | `x & y` |
| `|` | Bitwise OR | Sets bit to 1 if one bit is 1 | `x | y` |
| `^` | Bitwise XOR | Sets bit to 1 if only one bit is 1 | `x ^ y` |
| `<<` | Left shift | Shift left by pushing zeros | `x << 2` |
| `>>` | Right shift | Shift right | `x >> 2` |

---

## Conditionals


if condition {
    // ...
}

if condition {
    // ...
} else {
    // ...
}

if condition1 {
    // ...
} else if condition2 {
    // ...
} else {
    // ...
}


Nested `if` is allowed.

---

## Switch


switch expression {
case x:
    // ...
case y:
    // ...
default:
    // ...
}


**Example with `fallthrough`:**


day := 4
switch day {
case 1:
    fmt.Println("Monday")
    fallthrough  // checks next case even if true
case 2:
    fmt.Println("Tuesday")
case 3:
    fmt.Println("Wednesday")
case 4:
    fmt.Println("Thursday")
case 5:
    fmt.Println("Friday")
case 6:
    fmt.Println("Saturday")
case 7:
    fmt.Println("Sunday")
default:
    fmt.Println("Not a weekday")
}


**Multi-case:**


switch expr {
case x, y:
    // ...
case v, w:
    // ...
default:
    // ...
}


---

## Goto


Loop:
    // statements
    goto Loop


---

## For Loop


for i := 0; i < 5; i++ {
    fmt.Println(i)
}


- `continue` skips to next iteration
- `break` terminates the loop

**Range:**


for index, value := range fruits {
    fmt.Printf("%v\t%v\n", index, value)
}


**Example:**


package main
import ("fmt")

func main() {
    fruits := string{"apple", "orange", "banana"}[11]
    for index, val := range fruits {
        fmt.Printf("%v\t%v\n", index, val)
    }
}


---

## Functions


func FunctionName(param1 type, param2 type) {
    // code
}

FunctionName(val1, val2)


**With return:**


func myFunction(x int, y int) (result int) {
    result = x + y
    return result
}


### Recursion

- `testcount()` increments with each recursive call
- `factorial_recursion()` decrements argument each call

---

## Structs

**Declaration:**


type Person struct {
    name   string
    age    int
    job    string
    salary int
}


**Pointers:**


ptr := new(int)
*ptr = 90  // value
&ptr       // address of pointer


**Access members:**


var pers1 Person
pers1.name = "Hege"
pers1.age = 45
pers1.job = "Teacher"
pers1.salary = 6000


**Print via function:**


func printPerson(pers Person) {
    fmt.Println("Name:", pers.name)
    fmt.Println("Age:", pers.age)
    fmt.Println("Job:", pers.job)
    fmt.Println("Salary:", pers.salary)
}


**Example:**


package main
import ("fmt")

type Person struct {
    name   string
    age    int
    job    string
    salary int
}

func main() {
    var pers1 Person
    var pers2 Person

    // Pers1 specification
    pers1.name = "Hege"
    pers1.age = 45
    pers1.job = "Teacher"
    pers1.salary = 6000

    // Pers2 specification
    pers2.name = "Cecilie"
    pers2.age = 24
    pers2.job = "Marketing"
    pers2.salary = 4500

    printPerson(pers1)
    printPerson(pers2)
}

func printPerson(pers Person) {
    fmt.Println("Name:", pers.name)
    fmt.Println("Age:", pers.age)
    fmt.Println("Job:", pers.job)
    fmt.Println("Salary:", pers.salary)
}


---

## Maps

**Create with literals:**


var a = map[string]string{"brand": "Ford", "model": "Mustang", "year": "1964"}
b := map[string]int{"Oslo": 1, "Bergen": 2, "Trondheim": 3, "Stavanger": 4}


**Output:**

a    map[brand:Ford model:Mustang year:1964]
b    map[Bergen:2 Oslo:1 Stavanger:4 Trondheim:3]


**With `make`:**


var a = make(map[string]string)
a["brand"] = "Ford"
a["model"] = "Mustang"
a["year"] = "1964"

b := make(map[string]int)
b["Oslo"] = 1
b["Bergen"] = 2
b["Trondheim"] = 3
b["Stavanger"] = 4


**Empty map:**


var a = make(map[KeyType]ValueType)


**Access:**


value := a["brand"]


**Delete:**


delete(map_name, key)


**Iterate:**


for k, v := range a {
    fmt.Printf("%v : %v, ", k, v)
}


**Result:**

two : 2, three : 3, four : 4, one : 1,


---

## Goroutines

A goroutine is a lightweight thread managed by the Go runtime. Allows concurrent function execution.

**Syntax:**


go functionName()


**Example:**


package main
import (
    "fmt"
    "time"
)

func printNumbers() {
    for i := 1; i <= 5; i++ {
        fmt.Println("Number:", i)
        time.Sleep(500 * time.Millisecond)
    }
}


---

## Channels

A channel is a communication mechanism between goroutines.

**Syntax:**


ch := make(chan DataType)


**Send & Receive:**


ch <- value      // Send value to channel
val := <-ch      // Receive value from channel


**Simple example:**


package main
import "fmt"

func sendData(ch chan string) {
    ch <- "Hello from Goroutine!"
}

func main() {
    ch := make(chan string)
    go sendData(ch)
    msg := <-ch
    fmt.Println(msg)
}


**Output:**

Hello from Goroutine!


### Buffered Channels


ch := make(chan int, 3) // buffer of size 3

ch <- 10
ch <- 20
ch <- 30
fmt.Println(<-ch)
fmt.Println(<-ch)
fmt.Println(<-ch)


**Channels + Goroutines Example:**


package main
import "fmt"

func add(a, b int, ch chan int) {
    result := a + b
    ch <- result
}

func main() {
    ch := make(chan int)
    go add(10, 20, ch)
    go add(5, 15, ch)
    x := <-ch
    y := <-ch
    fmt.Println("Results:", x, y)
}


**Output:**

Results: 30 20


---

## Error Handling & Exceptions

**Syntax:**


value, err := someFunction()
if err != nil {
    // handle the error
}


**Example:**


package main
import (
    "errors"
    "fmt"
)

func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("cannot divide by zero")
    }
    return a / b, nil
}

func main() {
    result, err := divide(10, 0)
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Println("Result:", result)
}


**Custom Error Message:**


package main
import "fmt"

func readFile(filename string) error {
    return fmt.Errorf("failed to open file: %s", filename)
}

func main() {
    err := readFile("data.txt")
    if err != nil {
        fmt.Println("Error occurred:", err)
    }
}


### Using panic and recover

**panic:** Immediately stops normal execution. Used for unrecoverable errors.

**recover:** Used inside a defer block to regain control after a panic.


package main
import "fmt"

func riskyOperation() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered from panic:", r)
        }
    }()

    fmt.Println("Starting risky operation...")
    panic("something went wrong!")
    fmt.Println("This line will not execute")
}

func main() {
    riskyOperation()
    fmt.Println("Program continues normally after recovery.")
}


---

## File Handling

**Read file:**


package main
import (
    "bufio"
    "fmt"
    "os"
)

func main() {
    file, err := os.Open("example.txt")
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    defer file.Close()

    scanner := bufio.NewScanner(file)
    for scanner.Scan() {
        fmt.Println(scanner.Text())
    }

    if err := scanner.Err(); err != nil {
        fmt.Println("Error reading file:", err)
    }
}


**Append to file:**


package main
import (
    "fmt"
    "os"
)

func main() {
    file, err := os.OpenFile("output.txt", os.O_APPEND|os.O_WRONLY, 0644)
    if err != nil {
        fmt.Println("Error opening file:", err)
        return
    }
    defer file.Close()

    _, err = file.WriteString("\nAppended line using Go!")
    if err != nil {
        fmt.Println("Error writing to file:", err)
        return
    }

    fmt.Println("Data appended successfully!")
}


---

## Built-in Functions

| Function | Description | Example |
|----------|-------------|---------|
| `len()` | Returns length | `len(slice)` |
| `cap()` | Returns capacity | `cap(slice)` |
| `append()` | Adds elements to slice | `append(s, x)` |
| `copy()` | Copies slice elements | `copy(dst, src)` |
| `make()` | Allocates slice/map/channel | `make([]int, 5)` |
| `new()` | Allocates memory | `new(int)` |
| `delete()` | Removes map key | `delete(m, key)` |
| `close()` | Closes channel | `close(ch)` |
| `panic()` | Triggers runtime panic | `panic("error")` |
| `recover()` | Handles panic | `recover()` |
| `print()` / `println()` | Prints text (debug) | `println("Hi")` |
| `complex()` | Makes complex number | `complex(3,4)` |
| `real()` / `imag()` | Extract real/imag part | `real(c)` |

---

## Which Subjects Are Go Relevant For?

**Backend Development:** Go is excellent for building server-side applications.

**Cloud Computing:** Go is widely used in cloud infrastructure.

**System Programming:** Go provides low-level system access.

**Microservices:** Go excels at building microservices.

**DevOps:** Go is popular for DevOps tooling.

**Network Programming:** Go has strong networking capabilities.

**Concurrent Programming:** Go makes concurrent programming simple.

---

## Go Interview Questions

### 1. What is Go programming language, and why is it used?

Go is a modern programming language developed by Google. It is designed to be simple, efficient, and reliable. It is often used for building scalable and highly concurrent applications. It combines the ease of use of a high-level language with the performance of a low-level language. Go's syntax is easy to understand and its standard library provides a wide range of functionalities.

Additionally, Go has built-in support for concurrency, making it ideal for developing applications that require dealing with multiple tasks simultaneously. Overall, Go is used for developing fast, efficient, and robust software, especially in the field of web development and cloud computing.

### 2. How do you implement concurrency in Go?

In Go, concurrency is implemented using goroutines and channels. Goroutines are lightweight threads that can be created with the go keyword. They allow concurrent execution of functions.

Channels, on the other hand, are used for communication and synchronization between goroutines. They can be created using make and can be used to send and receive values.

To start a goroutine, simply prefix a function call with the go keyword. This will create a new goroutine that executes the function concurrently. Channels can be used to share data between goroutines, allowing them to communicate and synchronize.

By using goroutines and channels, Go provides a simple and efficient way to implement concurrency in programs.

### 3. How do you handle errors in Go?

In Go, errors are handled using the error type. When a function encounters an error, it can return an error value indicating the problem. The calling code can then check if the error is nil. If not, it handles the error appropriately.

Go provides a built-in panic function to handle exceptional situations. When a panic occurs, it stops the execution of the program and starts unwinding the stack, executing deferred functions along the way. To recover from a panic, you can use the recover function in a deferred function. This allows you to handle the panic gracefully and continue the program execution.

### 4. How do you implement interfaces in Go?

Interfaces are implemented implicitly in Go. This means that you don't need to explicitly declare that a type implements an interface. Instead, if a type satisfies all the methods defined in an interface, it is considered to implement that interface.

This is done by first defining the interface type using the type keyword followed by the interface name and the methods it should contain. The next step is creating a struct type or any existing type that has all the methods required by the interface. Go compiler will automatically recognize that type as implementing the interface.

Using interfaces can help you achieve greater flexibility and polymorphism in Go programs.

### 5. How do you optimize the performance of Go code?

These strategies can optimize Go code performance:

- Minimize memory allocations: Avoid unnecessary allocations by reusing existing objects or using buffers.
- Use goroutines and channels efficiently: Leverage the power of concurrent programming, but ensure proper synchronization to avoid race conditions.
- Optimize loops and conditionals: Reduce the number of iterations by simplifying logic or using more efficient algorithms.
- Profile your code: Use Go's built-in profiling tools to identify bottlenecks and hotspots in your code.

### 6. What is the role of the "init" function in Go?

The "init" function is a special function in Go that is used to initialize global variables or perform any other setup tasks needed by a package before it is used. The init function is called automatically when the package is first initialized. Its execution order within a package is not guaranteed.

Multiple init functions can be defined within a single package and even within a single file. This allows for modular and flexible initialization of package-level resources. Overall, the init function plays a crucial role in ensuring that packages are correctly initialized and ready to use when they are called.

### 7. What are dynamic and static types of declaration of a variable in Go?

The compiler must interpret the type of variable in a dynamic type variable declaration based on the value provided to it. The compiler does not consider it necessary for a variable to be typed statically.

Static type variable declaration assures the compiler that there is only one variable with the provided type and name, allowing the compiler to continue compiling without having all of the variable's details. A variable declaration only has meaning when the program is being compiled; the compiler requires genuine variable declaration when the program is being linked.

### 8. What is the syntax for declaring a variable in Go?

In Go, variables can be declared using the var keyword followed by the variable name, type, and optional initial value. For example:


var age int = 29


Go also allows short variable declaration using the := operator, which automatically infers the variable type based on the assigned value. For example:


age := 29


In this case, the type of the variable is inferred from the value assigned to it.

### 9. What are Golang packages?

Go Packages (abbreviated pkg) are simply directories in the Go workspace that contain Go source files or other Go packages. Every piece of code created in the source files, from variables to functions, is then placed in a linked package. Every source file should be associated with a package.

### 10. What are the different types of data types in Go?

The various data types in Go are:

- Numeric types: Integers, floating-point, and complex numbers.
- Boolean types: Represents true or false values.
- String types: Represents a sequence of characters.
- Array types: Stores a fixed-size sequence of elements of the same type.
- Slice types: Serves as a flexible and dynamic array.
- Struct types: Defines a collection of fields, each with a name and a type.
- Pointer types: Holds the memory address of a value.
- Function types: Represents a function.

### 11. How do you create a constant in Go?

To create a constant in Go, you can use the const keyword, followed by the name of the constant and its value. The value must be a compile-time constant such as a string, number, or boolean. Here's an example:


const Pi = 3.14159


After defining a constant, you can use it in your code throughout the program. Note that constants cannot be reassigned or modified during the execution of the program.

Creating constants allows you to give meaningful names to important values that remain constant throughout your Go program.

### 12. What data types does Golang use?

Golang uses the following types:

- Slice
- Struct
- Pointer
- Function
- Method
- Boolean
- Numeric
- String
- Array
- Interface
- Map
- Channel

### 13. Distinguish unbuffered from buffered channels.

The sender will block on an unbuffered channel until the receiver receives data from the channel, and the receiver will block on the channel until the sender puts data into the channel.

The sender of the buffered channel will block when there is no empty slot on the channel, however, the receiver will block on the channel when it is empty, as opposed to the unbuffered equivalent.

### 14. Explain string literals.

A string literal is a character-concatenated string constant. Raw string literals and interpreted string literals are the two types of string literals. Raw string literals are enclosed in backticks (`) and contain uninterpreted UTF-8 characters. Interpreted string literals are strings that are written within double quotes and can contain any character except newlines and unfinished double-quotes.

### 15. What is a Goroutine and how do you stop it?

A Goroutine is a function or procedure that runs concurrently with other Goroutines on a dedicated Goroutine thread. Goroutine threads are lighter than ordinary threads, and most Golang programs use thousands of goroutines at the same time.

A Goroutine can be stopped by passing it a signal channel. Because Goroutines can only respond to signals if they are taught to check, you must put checks at logical places, such as at the top of your for a loop.

### 16. What is the syntax for creating a function in Go?

To create a function in Go, you need to use the keyword func, followed by the function name, any parameter(s) enclosed in parentheses, and any return type(s) enclosed in parentheses. The function body is enclosed in curly braces {}.

Here is an example function that takes two integers as input and returns their sum:

We declare a function called add that takes two parameters, x and y, and returns their sum as an int.

### 17. How do you create a loop in Go?

The most commonly used loop is the for loop. It has three components: the initialization statement, the condition statement, and the post statement.

Here is an example of a for loop:

In this example, the loop will iterate 10 times. You can modify the i, condition, and post statement to customize the loop behavior.

### 18. What is the syntax for an if statement in Go?

The syntax for an if statement in Go is straightforward and similar to other programming languages. The if keyword is followed by a condition enclosed in parentheses, and the body of the statement is enclosed in curly braces.

This code block compares variables a and b and prints a message depending on their values. The condition is evaluated, and if it's true, the code inside the curly braces is executed. If it's false, the program skips to the else statement.

### 19. What are some benefits of using Go?

Go is an attempt to create a new, concurrent, garbage-collected language with quick compilation and the following advantages:

- On a single machine, a big Go application can be compiled in a matter of seconds.
- Go provides an architecture for software development that simplifies dependency analysis while avoiding much of the complexity associated with C-style programs, such as files and libraries.
- Because there is no hierarchy in Go's type system, no work is wasted describing the relationships between types. Furthermore, while Go uses static types, the language strives to make types feel lighter weight than in traditional OO languages.
- Go is fully garbage-collected and supports parallel execution and communication at a fundamental level.
- Go's design presents a method for developing system software on multicore processors.

### 20. How do you create a pointer in Go?

You can use the & symbol, followed by a variable to create a pointer in Go. This returns the memory address of the variable. For example, if you have a variable num of type int, you can create a pointer to num like this:


var num int = 42
var ptr *int = &num


Here, ptr is a pointer to num. You can use the * symbol to access the value stored in the memory address pointed by a pointer. For instance, *ptr will give you the value 42. Pointers are useful for efficient memory sharing and passing references between functions.

### 21. What is the syntax for creating a struct in Go?

You need to define a blueprint for the struct, which may consist of fields of different data types. The blueprint for the struct is defined using the 'type' keyword, followed by the name you want to give the struct.

You then use the 'struct' keyword, followed by braces ('{}') where you list the fields, each with a name and a data type separated by a comma.

### 22. How do you create an array in Go?

Creating an array in Go is simple. First, you need to declare the array by specifying its type and size. You can do this by using the following syntax:


var myArray [size]datatype


Replace size and datatype with the size and data type you want to use for your array. After declaring the array, you can then initialize it by assigning values to each index. You can also access and modify elements of the array using their index number.

Arrays in Go have fixed sizes, meaning you cannot add or remove elements once they are declared.

### 23. How will you perform inheritance with Golang?

This is a trick golang interview question because Golang does not support classes, hence there is no inheritance.

However, you may use composition to imitate inheritance behavior by leveraging an existing struct object to establish the initial behavior of a new object. Once the new object is created, the functionality of the original struct can be enhanced.

### 24. How do you create a slice in Go?

You first need to define a variable of type slice using the make() function. The make() function takes the type of slice, length, and capacity as parameters.


