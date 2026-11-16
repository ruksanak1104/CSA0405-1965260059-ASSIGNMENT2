# Multithreading in a Traffic-Camera Number-Plate Recognition System


## 1. Introduction

A thread is the smallest unit of execution within a process. Multiple
threads can execute different tasks simultaneously while sharing the
resources of the same process.

In a traffic-camera number-plate recognition system, 64 cameras capture
vehicle images continuously. Processing all camera frames one after
another takes more time and may cause delays during peak hours.

Using POSIX threads (Pthreads) allows multiple camera frames to be
processed concurrently, improving performance and reducing processing
time.

## 2. Resources Shared by Threads and Individual Resources

Threads belonging to the same process share certain resources, while
each thread maintains its own execution information.

  Shared Resources   Individual Resources
  ------------------ ----------------------
  Code section       Thread ID
  Data section       Program counter
  Heap memory        CPU registers
  Open files         Stack
  Global variables   Thread state

### Explanation

-   Shared resources allow threads to communicate easily.
-   Each thread has its own stack and registers.
-   Threads can access common data, so synchronisation is required when
    multiple threads modify shared variables.

## 3. Types of Threads

### A. User-Level Threads

User-level threads are managed by a user-level thread library without
direct kernel management of each thread.

**Advantages:**

-   Fast thread creation.
-   Fast context switching.
-   Does not require frequent kernel involvement.

**Disadvantages:**

-   A blocking system call may block the entire process in many-to-one
    implementations.
-   Limited parallel execution in the many-to-one model.

### B. Kernel-Level Threads

Kernel-level threads are managed directly by the operating system
kernel.

**Advantages:**

-   Multiple threads can execute on multiple CPU cores.
-   If one thread blocks, other threads can continue.
-   Better support for parallel processing.

**Disadvantages:**

-   Thread creation and context switching may involve higher overhead.
-   Requires kernel management.

## 4. Multithreading Models

  -----------------------------------------------------------------------
  Model             Description       Advantage         Disadvantage
  ----------------- ----------------- ----------------- -----------------
  Many-to-One       Many user threads Simple and fast   No true
                    map to one kernel management        parallelism
                    thread                              

  One-to-One        Each user thread  Supports          Higher resource
                    maps to one       parallelism       overhead
                    kernel thread                       

  Many-to-Many      Many user threads Flexible and      More complex
                    map to multiple   scalable          management
                    kernel threads                      
  -----------------------------------------------------------------------

**Evaluation:**

For a traffic-camera system, the one-to-one model is suitable because
multiple camera-processing threads can execute simultaneously on
multiple CPU cores.

## 5. POSIX Threads Implementation

POSIX Threads (Pthreads) is a standard library used to create and manage
threads in C programming.

The assignment requires processing 64 camera frames using:

1.  One thread per camera (64 threads).
2.  A pool of 4 threads.
3.  A pool of 8 threads.
4.  A pool of 16 threads.

The execution time is measured for each method and compared with
sequential processing.



### Compile and Run

Save the program as `thread_processing.c`.

Compile using:

``` bash
gcc thread_processing.c -o thread_processing -pthread
```

Run sequential processing:

``` bash
./thread_processing 1
```

Run using different thread configurations:

``` bash
./thread_processing 4
./thread_processing 8
./thread_processing 16
./thread_processing 64
```

Record the execution time for each configuration.

## 7. Speed-Up Calculation

Speed-up measures how much faster parallel processing is compared with
sequential processing.

\[ `\text{Speed-up}`{=tex} =
`\frac{\text{Sequential Time}}{\text{Parallel Time}}`{=tex} \]

**Example:**

If sequential processing takes 8 seconds and parallel processing takes 2
seconds:

\[ `\text{Speed-up}`{=tex} = `\frac{8}{2}`{=tex} = 4 \]

Therefore, the parallel system is four times faster.

### Record Your Results

  Configuration    Execution Time     Speed-up
  ---------------- ------------------ -----------
  Sequential (1)   Your measurement   1.00
  4 threads        Your measurement   Calculate
  8 threads        Your measurement   Calculate
  16 threads       Your measurement   Calculate
  64 threads       Your measurement   Calculate

## 8. Why Does Speed-Up Stop Increasing?

Increasing the number of threads does not always increase performance.

The reasons are:

1.  **CPU core limitation:** The number of available CPU cores limits
    simultaneous execution.
2.  **Context switching:** More threads increase scheduling overhead.
3.  **Thread creation:** Creating and managing many threads requires
    additional resources.
4.  **Synchronisation:** Threads may wait for mutex locks before
    accessing shared resources.
5.  **Memory contention:** Multiple threads accessing memory
    simultaneously can reduce performance.

**Evaluation:** A thread pool of 4, 8 or 16 threads may perform better
than creating 64 threads, depending on the processor and workload.

## 9. Shared Counter Synchronisation

When multiple threads update a shared counter without protection, a race
condition may occur.

For example:

``` c
count++;
```

If two threads execute this statement simultaneously, both may read the
same old value and overwrite each other's updates.

This can produce an incorrect final count.

### Solution: Use a Mutex

Use a mutex to protect the shared counter:

``` c
pthread_mutex_lock(&lock);

count++;

pthread_mutex_unlock(&lock);
```

This ensures that only one thread updates the counter at a time.

**Evaluation:** Mutex synchronisation prevents race conditions and
ensures data consistency. However, excessive locking may reduce
performance.

## 10. Alternative Concept -- Multiple Processes

If the 64 camera frames were processed using separate processes instead
of threads, each camera-processing task would execute in its own
process.

### Comparison: Threads vs Processes

  -----------------------------------------------------------------------
  Feature                 Threads                 Processes
  ----------------------- ----------------------- -----------------------
  Memory                  Share address space     Separate address spaces

  Creation                Generally faster        Generally slower

  Context switching       Generally lower         Generally higher
                          overhead                overhead

  Communication           Shared variables        IPC mechanisms

  Synchronisation         Mutexes and semaphores  IPC synchronisation

  Fault isolation         Less isolation          Better isolation
  -----------------------------------------------------------------------

**Evaluation:**

-   Threads use less memory because they share the same process address
    space.
-   Processes require separate address spaces.
-   Threads communicate easily using shared variables.
-   Processes require IPC mechanisms such as pipes or shared memory.
-   Threads are suitable for camera-frame processing when tasks need to
    share data.
-   Processes provide stronger fault isolation.

## 11. Time and Space Complexity Analysis

### Time Complexity

If 64 camera frames require equal processing work, the total processing
workload is proportional to the number of cameras.

\[ 64 `\times `{=tex}k \]

Where `k` represents the work required to process one camera frame.

For `n` cameras, the total sequential workload is:

\[ O(nk) \]

Parallel processing can reduce execution time depending on the number of
available CPU cores, synchronisation overhead and workload distribution.

### Space Complexity

-   Each thread requires its own stack and thread-management
    information.
-   Threads share the process's code, data and heap memory.
-   The results array requires `O(n)` space for `n` cameras.
-   The worker pool requires `O(t)` thread-management space for `t`
    worker threads.

**Evaluation:** Thread pools are more memory-efficient than creating a
very large number of independent processes because threads share the
same address space.

## 12. Conclusion

Multithreading improves the performance of traffic-camera number-plate
recognition systems by allowing multiple camera frames to be processed
concurrently. POSIX threads support efficient resource sharing and
parallel execution.

A thread pool of 4, 8 or 16 threads can reduce thread-management
overhead compared with creating 64 threads. The actual best
configuration depends on the available CPU cores and workload.

Therefore, a fixed thread pool is recommended for the traffic-camera
system because it provides efficient resource utilisation, controlled
thread creation and better scalability.

------------------------------------------------------------------------



