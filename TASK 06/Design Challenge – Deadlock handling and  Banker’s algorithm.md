# Deadlocks and Banker's Algorithm

## 1. Aim

To understand deadlocks in operating systems and implement Banker's
Algorithm in C to check system safety and decide whether resource
requests can be granted.

## 2. Theory

### A. Deadlock

A deadlock is a situation in which two or more processes wait
indefinitely for resources held by one another.

**Example:** Process P1 holds a printer and waits for a scanner, while
process P2 holds the scanner and waits for the printer. Neither process
can continue.

### B. Four Necessary Conditions for Deadlock

Deadlock can occur when all four conditions exist simultaneously:

1.  **Mutual Exclusion:** A resource can be used by only one process at
    a time.
2.  **Hold and Wait:** A process holds one resource while waiting for
    another.
3.  **No Preemption:** Resources cannot be forcibly taken away from a
    process.
4.  **Circular Wait:** A circular chain of processes exists, where each
    process waits for a resource held by the next process.

### C. Deadlock Handling Methods

  -----------------------------------------------------------------------
  Method                              Description
  ----------------------------------- -----------------------------------
  Deadlock Prevention                 Prevents at least one of the four
                                      necessary conditions.

  Deadlock Avoidance                  Grants requests only when the
                                      system remains in a safe state.

  Deadlock Detection                  Allows deadlocks to occur and
                                      checks for them.

  Deadlock Recovery                   Recovers by terminating processes
                                      or preempting resources.
  -----------------------------------------------------------------------

### D. Banker's Algorithm

Banker's Algorithm is a deadlock-avoidance algorithm. It checks whether
granting a resource request keeps the system in a safe state.

Important data structures:

-   **Available:** Number of available instances of each resource.
-   **Max:** Maximum resource demand of each process.
-   **Allocation:** Resources currently allocated to each process.
-   **Need:** Remaining resources required by each process.

**Formula:**

``` text
Need = Max - Allocation
```

A system is safe if there is at least one sequence in which every
process can complete using the currently available resources and
resources released by previously completed processes.

## 3. Given Data

-   Number of processes = **5**
-   Number of resource types = **3**

### Available Resources

    Resource A   Resource B   Resource C
  ------------ ------------ ------------
             3            3            2

### Maximum Need Matrix

  Process     A   B   C
  --------- --- --- ---
  P0          7   5   3
  P1          3   2   2
  P2          9   0   2
  P3          2   2   2
  P4          4   3   3

### Allocation Matrix

  Process     A   B   C
  --------- --- --- ---
  P0          0   1   0
  P1          2   0   0
  P2          3   0   2
  P3          2   1   1
  P4          0   0   2

## 4. Need Matrix Calculation

Using:

``` text
Need = Max - Allocation
```

  Process     A   B   C
  --------- --- --- ---
  P0          7   4   3
  P1          1   2   2
  P2          6   0   0
  P3          0   1   1
  P4          4   3   1

## 5. Safety Algorithm

Initial Available:

``` text
Work = [3, 3, 2]
```

The system checks which process has `Need <= Work`. When a process can
finish, its allocated resources are returned to `Work`.

    Step Process   Need      Work before execution   Work after execution
  ------ --------- --------- ----------------------- ----------------------
       1 P1        1, 2, 2   3, 3, 2                 5, 3, 2
       2 P3        0, 1, 1   5, 3, 2                 7, 4, 3
       3 P4        4, 3, 1   7, 4, 3                 7, 4, 5
       4 P0        7, 4, 3   7, 4, 5                 7, 5, 5
       5 P2        6, 0, 0   7, 5, 5                 10, 5, 7

**Safe sequence:**

``` text
P1 -> P3 -> P4 -> P0 -> P2
```

Therefore, the system is in a **safe state**.

## 6. Resource Request Evaluation

The requests are evaluated in order. A request that is denied or must
wait does not change the system's resource state.

### Request 1: P1 requests \[1, 0, 2\]

Initial Available:

``` text
[3, 3, 2]
```

The request is less than or equal to both the available resources and
P1's remaining need.

After tentatively granting the request:

``` text
Available = [2, 3, 0]
```

The system remains safe.

**Result:** Request granted.

One safe sequence is:

``` text
P1 -> P3 -> P4 -> P0 -> P2
```

### Request 2: P4 requests \[3, 3, 0\]

After granting P1's request, Available is:

``` text
[2, 3, 0]
```

P4 requests:

``` text
[3, 3, 0]
```

The request exceeds the available amount of resource A (`3 > 2`).

**Result:** Request cannot be granted immediately. P4 must wait. The
system state remains unchanged.

### Request 3: P0 requests \[0, 2, 0\]

After the first request, and with the second request still waiting,
Available is:

``` text
[2, 3, 0]
```

P0's request is within its remaining need and the currently available
resources. If tentatively granted:

``` text
Available = [2, 1, 0]
```

The resulting state is unsafe because no unfinished process has a
remaining need that can be satisfied by `[2, 1, 0]`.

**Result:** Request denied because it makes the system unsafe. The
tentative allocation is rolled back.


### Compile and Run

Save the program as `bankers_algorithm.c`.

``` bash
gcc bankers_algorithm.c -o bankers_algorithm
./bankers_algorithm
```

On Windows with GCC:

``` bash
gcc bankers_algorithm.c -o bankers_algorithm.exe
bankers_algorithm.exe
```

## 7. Resource Allocation Graph

A Resource Allocation Graph represents processes and resources.

-   **Process nodes** represent processes such as P0, P1 and P2.
-   **Resource nodes** represent resources such as A, B and C.
-   A **request edge** goes from a process to a resource.
-   An **allocation edge** goes from a resource to a process.

For example:

-   `P1 -> A` means P1 is requesting resource A.
-   `A -> P0` means resource A is allocated to P0.

A cycle in a resource allocation graph indicates a possible deadlock.
For single-instance resource types, a cycle indicates deadlock. With
multiple instances, a cycle alone is not sufficient to prove deadlock.

## 8. Alternative Solutions and Comparison

  -----------------------------------------------------------------------
  Method                  Advantages              Disadvantages
  ----------------------- ----------------------- -----------------------
  Prevention              Avoids deadlock by      Can reduce resource
                          breaking a necessary    utilisation
                          condition               

  Avoidance               Keeps the system in a   Requires maximum
                          safe state              resource demands

  Detection               Allows flexible         Deadlocks may occur
                          resource allocation     

  Recovery                Can restore the system  May require terminating
                          after deadlock          processes
  -----------------------------------------------------------------------

**Recommended approach:** For a research cluster where maximum resource
requirements are known, Banker's Algorithm can be used for deadlock
avoidance. For workloads with unpredictable resource requirements,
resource admission control combined with deadlock detection and recovery
may be more practical.

## 9. Time and Space Complexity

Let:

-   `n` = Number of processes.
-   `m` = Number of resource types.

### Time Complexity

``` text
O(n^2 × m)
```

The safety algorithm repeatedly checks processes and their resource
requirements.

### Space Complexity

``` text
O(n × m)
```

The program stores the Maximum, Allocation and Need matrices. The
additional `Work`, `Finish` and safe-sequence arrays require `O(n + m)`
space, which does not change the overall matrix-dominated bound.

## 10. Conclusion

Banker's Algorithm is used to avoid deadlocks by checking whether
resource allocation keeps the system in a safe state. The program
calculates the Need matrix, finds the safe sequence and evaluates
resource requests. It grants requests that preserve safety and rejects
requests that lead to an unsafe state.

