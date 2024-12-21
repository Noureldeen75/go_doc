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

---

### Lab-5-(part-2)

---

### Full Code:
```go
package main

import (
    "fmt"
    "sync"
    "time"
)

// Worker function
func worker(id int, tasks chan int, wg *sync.WaitGroup) {
    defer wg.Done()
    for task := range tasks {
        fmt.Printf("Worker %d processing task %d\n", id, task)
        time.Sleep(time.Second) // Simulate task processing time
    }
}

func main() {
    const numWorkers = 3
    const numReq = 10

    tasks := make(chan int, numReq) //Buffered Channel
    var wg sync.WaitGroup

    // Start workers
    for i := 1; i <= numWorkers; i++ {
        wg.Add(1)
        go worker(i, tasks, &wg)
    }

    // Send tasks to the task queue
    for i := 1; i <= numReq; i++ {
        tasks <- i
    }
    close(tasks) // Close the task channel to signal no more tasks

    // Wait for all workers to finish
    wg.Wait()

    fmt.Println("All tasks processed.")
}
```

---

### Explanation:

#### **Imports**
- `fmt`: For formatted input/output.
- `sync`: To manage synchronization with `WaitGroup`.
- `time`: To simulate task processing with delays.

---

#### **Worker Function**
```go
func worker(id int, tasks chan int, wg *sync.WaitGroup) {
    defer wg.Done()
    for task := range tasks {
        fmt.Printf("Worker %d processing task %d\n", id, task)
        time.Sleep(time.Second)
    }
}
```
- **Parameters**:
  - `id`: Worker ID for identification.
  - `tasks`: A channel from which tasks are received.
  - `wg`: A `WaitGroup` pointer to signal when the worker finishes.
- **Logic**:
  - `defer wg.Done()`: Ensures that the `WaitGroup` counter is decremented when the worker finishes.
  - `for task := range tasks`: Continuously retrieves tasks from the channel until it is closed.
  - `fmt.Printf`: Prints the worker ID and the task it is processing.
  - `time.Sleep`: Simulates task processing by pausing execution for 1 second.

---

#### **Main Function**
```go
const numWorkers = 3
const numReq = 10
```
- `numWorkers`: The number of workers (goroutines) to process tasks.
- `numReq`: The total number of tasks to be processed.

---

##### **Task Channel**
```go
tasks := make(chan int, numReq)
```
- A **buffered channel** is created with a capacity of `numReq` to hold tasks. This allows sending tasks without immediate consumption.

---

##### **Worker Initialization**
```go
for i := 1; i <= numWorkers; i++ {
    wg.Add(1)
    go worker(i, tasks, &wg)
}
```
- A loop starts `numWorkers` workers.
- `wg.Add(1)`: Increments the `WaitGroup` counter for each worker.
- `go worker`: Launches the worker function as a goroutine.

---

##### **Task Distribution**
```go
for i := 1; i <= numReq; i++ {
    tasks <- i
}
close(tasks)
```
- A loop sends `numReq` tasks to the `tasks` channel.
- `close(tasks)`: Signals workers that no more tasks will be sent.

---

##### **Waiting for Workers**
```go
wg.Wait()
```
- Blocks the main function until all workers signal completion using `wg.Done()`.

---

##### **Completion**
```go
fmt.Println("All tasks processed.")
```
- Prints a message after all tasks are processed and workers finish.

---

### **Execution Flow**
1. **Workers Start**:
   - `numWorkers` goroutines are created, each running the `worker` function.
2. **Tasks Sent**:
   - `numReq` tasks are sent to the `tasks` channel.
3. **Task Processing**:
   - Workers process tasks concurrently, retrieving tasks from the channel.
4. **Channel Closed**:
   - The `tasks` channel is closed after all tasks are sent.
5. **Wait for Completion**:
   - The main function waits for all workers to finish.
6. **Program Ends**:
   - A message is printed, and the program exits.

---

### **Key Concepts**
1. **Concurrency**:
   - Workers run as separate goroutines, enabling parallel task processing.
2. **Buffered Channel**:
   - Tasks are queued in a channel, decoupling task production from consumption.
3. **WaitGroup**:
   - Ensures the main function waits until all workers finish.
4. **Channel Closing**:
   - Properly closing the channel signals workers to stop waiting for tasks.

---

### **Example Output**
The order of task processing may vary due to concurrent execution:

```
Worker 1 processing task 1
Worker 2 processing task 2
Worker 3 processing task 3
Worker 1 processing task 4
Worker 2 processing task 5
Worker 3 processing task 6
Worker 1 processing task 7
Worker 2 processing task 8
Worker 3 processing task 9
Worker 1 processing task 10
All tasks processed.
```

---

### Lab-6
---

### Full Code

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

// Define Task struct
type Task struct {
	ID          int
	ProcessTime int
}

// Define a method of the Task struct
func (t *Task) Process() {
	fmt.Printf("Processing Task %d...\n", t.ID)
	time.Sleep(time.Duration(t.ProcessTime) * time.Second)
}

// Define WorkerPool struct
type WorkerPool struct {
	ID              int
	Tasks           []Task
	NumberOfWorkers int
	TasksChannel    chan Task
	WG              sync.WaitGroup
}

// Define a method on the WorkerPool struct for workers
func (w *WorkerPool) Worker(workerID int) {
	for task := range w.TasksChannel {
		fmt.Printf("Worker %d: ", workerID)
		task.Process()
		w.WG.Done()
	}
}

// Start the worker pool and distribute tasks
func (w *WorkerPool) Pool() {
	w.TasksChannel = make(chan Task, len(w.Tasks)) // Buffered channel with the size of the task list
	for i := 1; i <= w.NumberOfWorkers; i++ {
		go w.Worker(i) // Each worker gets a unique ID
	}

	for _, task := range w.Tasks {
		w.WG.Add(1)
		w.TasksChannel <- task // Send tasks to the channel
	}

	close(w.TasksChannel) // Close the channel to signal workers
	w.WG.Wait()           // Wait for all workers to finish
}

func main() {
	// Create a list of tasks
	tasks := make([]Task, 20)
	for i := 0; i < cap(tasks); i++ {
		tasks[i] = Task{
			ID:          i + 1,
			ProcessTime: 1, // Each task takes 1 second to process
		}
	}

	// Create a WorkerPool instance
	wp := WorkerPool{
		ID:              1, // Assign an ID to the WorkerPool
		Tasks:           tasks,
		NumberOfWorkers: 5, // Number of workers
	}

	// Start the worker pool
	wp.Pool()

	fmt.Println("All tasks completed")
}
```

---

### Explanation

#### **Task Struct**
```go
type Task struct {
	ID          int
	ProcessTime int
}
```
- **Fields**:
  - `ID`: Unique identifier for each task.
  - `ProcessTime`: Time (in seconds) required to process the task.
  
```go
func (t *Task) Process() {
	fmt.Printf("Processing Task %d...\n", t.ID)
	time.Sleep(time.Duration(t.ProcessTime) * time.Second)
}
```
- **`Process` Method**:
  - Simulates processing by printing the task ID and sleeping for the specified `ProcessTime`.

---

#### **WorkerPool Struct**
```go
type WorkerPool struct {
	ID              int
	Tasks           []Task
	NumberOfWorkers int
	TasksChannel    chan Task
	WG              sync.WaitGroup
}
```
- **Fields**:
  - `ID`: Unique identifier for the worker pool.
  - `Tasks`: A slice of `Task` objects to be processed.
  - `NumberOfWorkers`: Number of concurrent workers in the pool.
  - `TasksChannel`: A channel for distributing tasks to workers.
  - `WG`: A `WaitGroup` to ensure all workers finish before exiting.

---

#### **Worker Method**
```go
func (w *WorkerPool) Worker(workerID int) {
	for task := range w.TasksChannel {
		fmt.Printf("Worker %d: ", workerID)
		task.Process()
		w.WG.Done()
	}
}
```
- **Purpose**:
  - Processes tasks received from the `TasksChannel`.
- **Logic**:
  - Prints the worker ID and processes the task.
  - Calls `w.WG.Done()` to signal task completion.

---

#### **Pool Method**
```go
func (w *WorkerPool) Pool() {
	w.TasksChannel = make(chan Task, len(w.Tasks)) // Buffered channel
	for i := 1; i <= w.NumberOfWorkers; i++ {
		go w.Worker(i) // Assigns a unique ID to each worker
	}

	for _, task := range w.Tasks {
		w.WG.Add(1)       // Increment WaitGroup counter
		w.TasksChannel <- task // Send task to the channel
	}

	close(w.TasksChannel) // Close the channel after sending all tasks
	w.WG.Wait()           // Wait for all workers to finish
}
```
- **Purpose**:
  - Initializes workers and distributes tasks.
- **Logic**:
  - Creates a buffered channel to hold tasks.
  - Launches `NumberOfWorkers` workers.
  - Sends tasks to the channel.
  - Closes the channel and waits for workers to complete.

---

#### **Main Function**
```go
func main() {
	tasks := make([]Task, 20)
	for i := 0; i < cap(tasks); i++ {
		tasks[i] = Task{
			ID:          i + 1,
			ProcessTime: 1,
		}
	}

	wp := WorkerPool{
		ID:              1,
		Tasks:           tasks,
		NumberOfWorkers: 5,
	}

	wp.Pool()

	fmt.Println("All tasks completed")
}
```
- **Purpose**:
  - Creates tasks and a `WorkerPool` instance.
  - Starts the worker pool to process tasks.
- **Logic**:
  - Initializes 20 tasks, each taking 1 second to process.
  - Creates a `WorkerPool` with 5 workers.
  - Calls the `Pool` method to process tasks.

---

### **Execution Flow**
1. **Task Creation**:
   - 20 tasks are created with IDs from 1 to 20.
2. **WorkerPool Initialization**:
   - A `WorkerPool` with 5 workers is created.
3. **Worker Launch**:
   - 5 workers are started, each with a unique ID.
4. **Task Distribution**:
   - Tasks are sent to the `TasksChannel`.
5. **Task Processing**:
   - Workers retrieve and process tasks concurrently.
6. **Completion**:
   - The `WaitGroup` ensures all tasks are processed before the program exits.

---

### **Sample Output**
Order may vary due to concurrency:
```
Worker 1: Processing Task 1...
Worker 2: Processing Task 2...
Worker 3: Processing Task 3...
Worker 4: Processing Task 4...
Worker 5: Processing Task 5...
Worker 1: Processing Task 6...
Worker 2: Processing Task 7...
Worker 3: Processing Task 8...
Worker 4: Processing Task 9...
Worker 5: Processing Task 10...
Worker 1: Processing Task 11...
Worker 2: Processing Task 12...
Worker 3: Processing Task 13...
Worker 4: Processing Task 14...
Worker 5: Processing Task 15...
Worker 1: Processing Task 16...
Worker 2: Processing Task 17...
Worker 3: Processing Task 18...
Worker 4: Processing Task 19...
Worker 5: Processing Task 20...
All tasks completed
```