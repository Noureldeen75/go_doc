### Lab-1/2

---

### Package and Imports
```go
package main

import(
    "fmt"
    "time"
)
```

- **`package main`**: This defines the package as `main`, which is required for a Go program's entry point.
- **Imports**:
  - **`fmt`**: Provides formatted I/O operations, such as printing to the console and scanning user input.
  - **`time`**: Used for time-related operations like sleeping for a duration and measuring execution time.

---

### `copy_simulation` Function
```go
func copy_simulation(num int, done chan bool) {
    for i := 1; i <= num; i++ {
        fmt.Printf("-")
        time.Sleep(1000 * time.Millisecond)
    }
    done <- true
}
```

- **Purpose**: Simulates a copy operation by printing a dash (`-`) at regular intervals.
- **Parameters**:
  - `num`: The number of dashes to print.
  - `done`: A channel to signal completion of the operation.
- **Logic**:
  - A loop runs `num` times, printing a dash and pausing for 1 second (1000 milliseconds) each iteration.
  - After completing the loop, it sends `true` into the `done` channel to signal that the task is finished.

---

### `printLetters` Function
```go
func printLetters(char rune) {
    for i := 'A'; i <= char; i++ {
        fmt.Printf("%c ", i)
    }
}
```

- **Purpose**: Prints uppercase English letters from `A` to the specified character.
- **Parameters**:
  - `char`: The final character to print.
- **Logic**:
  - A loop iterates from `'A'` to the provided `char`, printing each character followed by a space.
  - **Note**: The commented-out `time.Sleep(500 * time.Millisecond)` line could be used to introduce a delay between prints.

---

### `factorial` Function
```go
func factorial(num int) int {
    fact := 1
    for i := 2; i <= num; i++ {
        fact *= i
    }
    return fact
}
```

- **Purpose**: Computes the factorial of a given number.
- **Parameters**:
  - `num`: The number for which the factorial is calculated.
- **Logic**:
  - Initializes `fact` to `1`.
  - Loops from `2` to `num`, multiplying `fact` by each value.
  - Returns the computed factorial.

---

### `main` Function
```go
func main() {
    now := time.Now()
    defer func() {
        fmt.Println("Execution time = ", time.Since(now))
    }()

    ch_signal := make(chan bool)
    go copy_simulation(20, ch_signal)

    var input_num int
    fmt.Printf("Enter Number: ")
    fmt.Scan(&input_num)

    answer := factorial(input_num)
    fmt.Printf("%d! = %d\n", input_num, answer)

    <-ch_signal
}
```

- **Purpose**: Entry point of the program, orchestrating the execution of tasks.
- **Key Sections**:
  - **Execution Time Measurement**:
    - Captures the current time with `time.Now()` and defers a function to calculate and print the elapsed time after `main` completes.
  - **Channel Initialization**:
    - Creates a `chan bool` named `ch_signal` for signaling between goroutines.
  - **Goroutine**:
    - Starts a concurrent execution of `copy_simulation` with 20 dashes.
  - **User Input**:
    - Prompts the user to enter a number, reads the input, and calculates its factorial using `factorial`.
  - **Synchronization**:
    - Waits for the `copy_simulation` goroutine to finish by reading from `ch_signal`.
  - **Output**:
    - Prints the factorial result.

---

### Execution Time
- The `defer` statement in the code ensures that the execution time is calculated and printed after the `main` function completes.
- It uses `time.Since(now)` to compute the time elapsed since the program started and outputs it to the console.

---

### Lab-3

---

### Imports
```go
import (
	"fmt"
	"math/rand"
	"time"
)
```

- **`fmt`**: Provides functions for formatted I/O, such as `fmt.Println` and `fmt.Scan`.
- **`math/rand`**: Provides functions for generating random numbers.
- **`time`**: Provides functions for working with time, such as `time.Now()`.

---

### `getMoney` Function Breakdown:
```go
func getMoney(ch chan int) {
	// Accept number of trials
	var trials int
	fmt.Println("Number of trials: ")
	if _, err := fmt.Scan(&trials); err != nil {
		fmt.Println("Please Provide Intege number")
		return
	}

	rand.Seed(time.Now().UnixNano()) // Pseudo Random
	for i := 1; i <= trials; i++ {
		amount := rand.Intn(500)
		ch <- amount
	} // End sending
	close(ch)
}
```

#### Function Signature:
```go
func getMoney(ch chan int)
```
- **Parameters**: 
  - `ch`: A channel of type `int` used to send random amounts back to the main function.

---

#### Input: Number of Trials
```go
var trials int
fmt.Println("Number of trials: ")
if _, err := fmt.Scan(&trials); err != nil {
	fmt.Println("Please Provide Intege number")
	return
}
```
- Prompts the user to input the number of trials.
- Reads the input using `fmt.Scan(&trials)` and stores it in the `trials` variable.
- If the input is invalid (not an integer), prints an error message and exits the function early.

---

#### Seeding the Random Number Generator:
```go
rand.Seed(time.Now().UnixNano()) // Pseudo Random
```
- Seeds the random number generator with the current time in nanoseconds to ensure unique random numbers for each run.

---

#### Generating and Sending Random Amounts:
```go
for i := 1; i <= trials; i++ {
	amount := rand.Intn(500)
	ch <- amount
}
```
- A loop runs `trials` times.
- **`rand.Intn(500)`**: Generates a random integer between 0 and 499.
- Sends the generated random amount through the channel `ch`.

---

#### Closing the Channel:
```go
close(ch)
```
- Signals that no more values will be sent through the channel.
- Important for the receiver to know when to stop waiting for data.

---

### `main` Function
```go
func main() {
	channel := make(chan int)
	go getMoney(channel)

	for msg := range channel { // check whether channel (sending)
		fmt.Printf("You've won: %d$\n", msg)
	}
}
```

#### Breakdown:
1. **Channel Creation**:
   ```go
   channel := make(chan int)
   ```
   - Creates a new channel of type `int`.

2. **Concurrent Execution**:
   ```go
   go getMoney(channel)
   ```
   - Runs the `getMoney` function concurrently in a separate goroutine.

3. **Receiving Data**:
   ```go
   for msg := range channel {
       fmt.Printf("You've won: %d$\n", msg)
   }
   ```
   - Listens for messages (random amounts) from the channel.
   - The loop ends when the channel is closed.

4. **Output**:
   ```go
   fmt.Printf("You've won: %d$\n", msg)
   ```
   - Prints the amount received from the channel.

---

### Execution Flow:
1. The `main` function creates a channel and starts the `getMoney` function in a goroutine.
2. The `getMoney` function prompts the user for the number of trials, generates random amounts, and sends them through the channel.
3. The `main` function listens for values from the channel and prints each one.
4. Once the channel is closed, the program terminates.

---

### Example Output:
If the user enters `3` as the number of trials, the output might look like this (order may vary due to concurrency):

```plaintext
Number of trials:
3
You've won: 250$
You've won: 120$
You've won: 499$
```

---

### Lab-4

---

### Code Breakdown:

#### 1. Package and Imports
```go
package main

import (
	"fmt"
	"sync"
)
```

- **`package main`**: Defines the entry point for the program.
- **`fmt`**: Used for formatted input/output, like printing to the console.
- **`sync`**: Provides synchronization primitives, such as `sync.WaitGroup`, to manage goroutines.

---

#### 2. `printNumber` Function
```go
func printNumber(num int, wg *sync.WaitGroup) {
	defer wg.Done() // Notify WaitGroup that this Goroutine is done
	fmt.Println(num)
}
```

- **`num int`**: The number to print.
- **`wg *sync.WaitGroup`**: A pointer to the `WaitGroup` used to track active goroutines.
- **`defer wg.Done()`**: Ensures that `wg.Done()` is called when the function exits, signaling that this goroutine is complete.
- **`fmt.Println(num)`**: Prints the number to the console.

---

#### 3. `main` Function
```go
func main() {
	var wg sync.WaitGroup
	for i := 1; i <= 5; i++ {
		wg.Add(1)              // Increment WaitGroup counter
		go printNumber(i, &wg) // Start Goroutine with WaitGroup
	}
	wg.Wait() // Wait for all Goroutines to complete
}
```

- **`var wg sync.WaitGroup`**: Creates a `WaitGroup` to track and wait for goroutines.
- **`for i := 1; i <= 5; i++`**: Loops from 1 to 5, creating a new goroutine for each iteration.
- **`wg.Add(1)`**: Increments the `WaitGroup` counter by 1 for each goroutine started.
- **`go printNumber(i, &wg)`**: Starts a new goroutine, passing the current number (`i`) and a pointer to the `WaitGroup`.
- **`wg.Wait()`**: Blocks the `main` function until all goroutines have called `wg.Done()`.

---

### Execution Flow:
1. The `main` function initializes a `WaitGroup` and starts 5 goroutines with the `printNumber` function.
2. Each goroutine prints a number and calls `wg.Done()` when finished.
3. The `main` function waits for all goroutines to complete using `wg.Wait()`.
4. The program exits after all numbers are printed.

---

### Key Concepts:

- **Concurrency**:
  - `go printNumber(i, &wg)` launches the `printNumber` function as a goroutine, enabling concurrent execution.

- **Synchronization with `sync.WaitGroup`**:
  - The `WaitGroup` ensures the `main` function waits until all goroutines finish.

- **Defer**:
  - `defer wg.Done()` guarantees that `wg.Done()` is called even if the function exits prematurely.

---

### Example Output:
The output will print numbers from 1 to 5, but the order may vary because goroutines execute concurrently. Example:

```plaintext
2
1
4
3
5
```
---

### Lab-5

---

### **Imports**
```go
import "fmt"
```

- **`fmt`**: Provides functions for formatted I/O, such as `fmt.Println` and `fmt.Printf`.

---

### **Commented Code Explanation**

The commented-out code demonstrates how slices work in Go, including their relationship with arrays and how modifying a slice affects the underlying array.

```go
number := [5]int{1, 2, 3, 4, 5}
fmt.Println(number) // [1, 2, 3, 4, 5]
```

- **`number`**:
  - An array of 5 integers initialized with values `[1, 2, 3, 4, 5]`.
  - Arrays in Go have a fixed size, defined at the time of declaration (`[5]int`).

- **`fmt.Println(number)`**:
  - Prints the array's content: `[1, 2, 3, 4, 5]`.

---

```go
slice := number[2:4] // len = 2, cap = 3
fmt.Print("Cap: ", cap(slice))
```

- **`slice := number[2:4]`**:
  - Creates a slice referencing the array `number` starting from index 2 (inclusive) to index 4 (exclusive).
  - The slice contains the elements `[3, 4]`.

- **`len(slice)`**:
  - The length of the slice is `2` because it includes two elements: `[3, 4]`.

- **`cap(slice)`**:
  - The capacity of the slice is `3` because it can extend up to the end of the original array (`[3, 4, 5]`).

- **`fmt.Print("Cap: ", cap(slice))`**:
  - Prints the slice's capacity: `Cap: 3`.

---

```go
slice[0] = -10
fmt.Println("new Slice", slice) // [-10, 4] 
fmt.Println("new Array", number) //[1, 2, -10, 4, 5]
```

- **`slice[0] = -10`**:
  - Modifies the first element of the slice (`slice[0]`).
  - Since slices are references to the underlying array, the change also affects the array `number`.

- **`fmt.Println("new Slice", slice)`**:
  - Prints the updated slice: `[-10, 4]`.

- **`fmt.Println("new Array", number)`**:
  - Prints the updated array: `[1, 2, -10, 4, 5]`.
  - Notice how modifying the slice also modified the original array.

---

### **Main Function with Struct Example**

The main function demonstrates how structs can be used to group related data.

```go
func main() {
	type Student struct {
		courses [5]string
		grades  [5]float64
	}
```

- **`Student`**:
  - A struct with two fields:
    - **`courses`**: An array of 5 strings for course names.
    - **`grades`**: An array of 5 floating-point numbers for course grades.

---

```go
	qassas := Student{
		courses: [5]string{"DB", "IT", "AI", "ML", "CN"},
		grades:  [5]float64{85.0, 90.0, 92.0, 95.5, 100.0},
	}
```

- **`qassas`**:
  - An instance of the `Student` struct.
  - Initialized using a struct literal:
    - `courses`: Contains 5 course names: `"DB"`, `"IT"`, `"AI"`, `"ML"`, `"CN"`.
    - `grades`: Contains 5 grades corresponding to the courses: `85.0`, `90.0`, `92.0`, `95.5`, and `100.0`.

---

```go
	fmt.Println("course: ", qassas.courses)
}
```

- **`fmt.Println("course: ", qassas.courses)`**:
  - Prints the `courses` field of the `qassas` struct.
  - Output: `course:  [DB IT AI ML CN]`.

---

### **Execution Flow**
1. **Commented Code**:
   - Demonstrates how slices reference arrays and how modifying a slice affects the original array.
   - Highlights key slice properties like `len` (length) and `cap` (capacity).

2. **Struct Example**:
   - Defines a `Student` struct to group course names and grades.
   - Creates an instance of the struct (`qassas`) with initialized values.
   - Accesses and prints the `courses` field.

---

### **Key Concepts**

#### **Slices and Arrays**
- Slices are dynamic views into arrays.
- Modifying a slice affects the underlying array.
- Slices have `len` (length) and `cap` (capacity), which may differ.

#### **Structs in Go**
- A struct is a composite type for grouping related fields.
- Fields are accessed using the `.` operator.

---

### **Output**

#### **Commented Code**
```plaintext
[1, 2, 3, 4, 5]
Cap: 3
new Slice [-10, 4]
new Array [1, 2, -10, 4, 5]
```

#### **Main Function**
```plaintext
course:  [DB IT AI ML CN]
```

### Lab-6

---
