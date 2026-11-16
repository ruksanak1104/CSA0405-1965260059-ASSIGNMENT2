# Process Synchronisation -- Producer-Consumer Problem Using Semaphores and Mutex Locks

## 1. Aim

To study process synchronisation and implement the producer-consumer
problem using semaphores and mutex locks to avoid race conditions.

## 2. Theory

Process synchronisation coordinates multiple processes or threads
accessing shared resources. It prevents incorrect results when multiple
threads execute at the same time.

### A. Critical Section

A critical section is the part of a program where shared data or
resources are accessed.

A synchronisation mechanism should satisfy three conditions:

1.  **Mutual Exclusion:** Only one process or thread can enter the
    critical section at a time.
2.  **Progress:** If no thread is inside the critical section, the
    selection of the next thread should not be delayed unnecessarily.
3.  **Bounded Waiting:** Every waiting thread must get an opportunity to
    enter the critical section within a limited number of turns.

### B. Peterson's Algorithm

Peterson's algorithm is a software-based method for achieving mutual
exclusion between two processes.

It uses:

-   `flag[2]` to indicate whether a process wants to enter the critical
    section.
-   `turn` to decide which process gets priority.

It satisfies mutual exclusion, progress and bounded waiting under its
assumed memory-ordering model. On modern processors, ordinary shared
variables are not sufficient without appropriate atomic operations and
memory ordering.

### C. Semaphore

A semaphore is a synchronisation variable used to control access to
shared resources.

**Types of semaphores:**

  Type                 Description
  -------------------- ------------------------------------------------
  Binary semaphore     Takes values 0 or 1; used for exclusive access
  Counting semaphore   Maintains a count of available resources

**Two main operations:**

-   `wait()` -- decreases the semaphore value or waits until a resource
    is available.
-   `post()` -- increases the semaphore value and may wake a waiting
    thread.

### D. Producer-Consumer Problem

The producer creates data and places it in a shared buffer. The consumer
removes data from the buffer.

Three synchronisation variables are used:

-   **empty:** Counts available buffer spaces.
-   **full:** Counts occupied buffer spaces.
-   **mutex:** Protects the shared buffer from simultaneous
    modification.

### E. Other Synchronisation Problems

**Readers-Writers Problem:** Multiple readers can read shared data
simultaneously, but writing must be exclusive.

**Dining Philosophers Problem:** Multiple philosophers compete for
shared forks. Poor synchronisation can cause deadlock.

**Monitor:** A high-level synchronisation mechanism that combines shared
data, mutual exclusion and condition variables.



**Compile and run (Linux):**

``` bash
gcc producer_consumer.c -o producer_consumer -pthread
./producer_consumer
```

To specify producer and consumer thread counts:

``` bash
./producer_consumer 1 1
./producer_consumer 4 4
```

> **Implementation note:** In this program, every producer creates 20
> items and every consumer attempts to consume 20 items. Therefore, use
> equal producer and consumer counts (for example, `1 1` or `4 4`).
> Unequal counts can cause the program to wait indefinitely or leave
> items unconsumed.

## 3. Evaluation of Synchronisation

### What happens if the mutex is removed?

Without the mutex:

-   Multiple producers may update the buffer index simultaneously.
-   Multiple consumers may read the same buffer position.
-   The shared sum and count may be updated incorrectly.
-   Some items may be lost or consumed more than once.

Semaphores control the number of occupied and empty slots, but they do
not by themselves protect every shared variable from simultaneous
updates.

### Locking Strategy for Railway Booking

  -----------------------------------------------------------------------
  Locking Method          Advantage               Limitation
  ----------------------- ----------------------- -----------------------
  Whole-system lock       Simple and safe         Low concurrency

  Train-level lock        Different trains can be Same train bookings
                          booked simultaneously   must wait

  Coach-level lock        More parallel booking   More complex
                          operations              synchronisation
  -----------------------------------------------------------------------

**Recommended approach:** Use train-level or coach-level locking with
appropriate protection for shared seat information. This allows
independent bookings to proceed concurrently while preventing double
booking.

## 4. Alternative Solution

An alternative is to use atomic operations or spinlocks.

  -----------------------------------------------------------------------
  Method                  Advantage               Limitation
  ----------------------- ----------------------- -----------------------
  Mutex                   Suitable for longer     Threads may block
                          critical sections       

  Semaphore               Controls access to      Requires correct
                          multiple resources      wait/post ordering

  Atomic operations       Efficient for simple    Not suitable for every
                          shared updates          complex operation

  Spinlock                Avoids sleeping for     Wastes CPU while
                          very short waits        waiting
  -----------------------------------------------------------------------

For simple counters, atomic operations such as `atomic_fetch_add()` can
be useful. For longer critical sections, a mutex is generally more
appropriate because a waiting thread does not continuously consume CPU
time.

## 5. Time and Space Complexity

Let `n` be the total number of items produced.

-   **Producing all items:** (O(n))
-   **Consuming all items:** (O(n))
-   **Buffer space:** (O(1)), because its size is fixed at 5.
-   **Thread management space:** (O(p+c)), where `p` is the number of
    producers and `c` is the number of consumers.

## 6. Conclusion

Process synchronisation is essential when multiple threads access shared
resources. Semaphores control buffer availability, while mutex locks
protect the critical section. The producer-consumer program demonstrates
how synchronisation prevents race conditions and ensures that every
produced item is consumed exactly once.

