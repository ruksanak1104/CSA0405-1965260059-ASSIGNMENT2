# CPU Scheduling in a Cloud Video-Encoding Service


## 1. Introduction

CPU scheduling is the process of selecting a process from the ready
queue and allocating the CPU to it.

In a cloud video-encoding service, short jobs (thumbnails) and long jobs
(full-length videos) arrive together. If long jobs occupy the CPU for a
long time, short jobs may experience delays.

CPU scheduling algorithms improve CPU utilisation, reduce waiting time
and provide better response time.

## 2. Given Process Details

The following processes are considered for CPU scheduling:

   Process    Arrival Time (AT)   Burst Time (BT)
  --------- ------------------- -----------------
     P1                       0                 8
     P2                       1                 4
     P3                       2                 9
     P4                       3                 5

**Time quantum for Round Robin:** 3 units.

For Priority Scheduling, since priority values are not provided, assume
ascending priority:

   Process    Priority
  --------- ----------
     P1              1
     P2              2
     P3              3
     P4              4

Smaller priority number means higher priority.

## 3. CPU Scheduling Algorithms

### A. First Come First Serve (FCFS)

FCFS is a non-preemptive scheduling algorithm in which the process that
arrives first gets the CPU first.

**Execution order:** `P1 → P2 → P3 → P4`

**Gantt Chart:**

``` text
        0          8       12          21       26
        ┌──────────┬───────┬───────────┬────────┐
        │    P1    │  P2   │    P3     │   P4   │
        └──────────┴───────┴───────────┴────────┘
```

**Calculation:**

   Process    AT   BT   CT   TAT   WT   RT
  --------- ---- ---- ---- ----- ---- ----
     P1        0    8    8     8    0    0
     P2        1    4   12    11    7    7
     P3        2    9   21    19   10   10
     P4        3    5   26    23   18   18

-   **Average Waiting Time:**

    \[ AWT = `\frac{0+7+10+18}{4}`{=tex} = 8.75 \]

-   **Average Turnaround Time:**

    \[ ATAT = `\frac{8+11+19+23}{4}`{=tex} = 15.25 \]

-   **Average Response Time:**

    \[ ART = `\frac{0+7+10+18}{4}`{=tex} = 8.75 \]

### B. Shortest Job First (SJF)

SJF is a non-preemptive scheduling algorithm that selects the process
with the shortest burst time among the available processes.

**Execution order:** `P1 → P2 → P4 → P3`

**Gantt Chart:**

``` text
        0          8       12     17           26
        ┌──────────┬───────┬──────┬────────────┐
        │    P1    │  P2   │ P4   │     P3     │
        └──────────┴───────┴──────┴────────────┘
```

**Calculation:**

   Process    AT   BT   CT   TAT   WT   RT
  --------- ---- ---- ---- ----- ---- ----
     P1        0    8    8     8    0    0
     P2        1    4   12    11    7    7
     P3        2    9   26    24   15   15
     P4        3    5   17    14    9    9

-   **Average Waiting Time:**

    \[ AWT = `\frac{0+7+15+9}{4}`{=tex} = 7.75 \]

-   **Average Turnaround Time:**

    \[ ATAT = `\frac{8+11+24+14}{4}`{=tex} = 14.25 \]

-   **Average Response Time:**

    \[ ART = `\frac{0+7+15+9}{4}`{=tex} = 7.75 \]

### C. Shortest Remaining Time First (SRTF)

SRTF is the preemptive version of SJF. It selects the process with the
shortest remaining burst time.

If a new process arrives with a shorter remaining time, the currently
running process is interrupted.

**Execution order:** `P1 → P2 → P4 → P1 → P3`

**Gantt Chart:**

``` text
        0    1        5       10       17          26
        ┌────┬────────┬───────┬────────┬───────────┐
        │ P1 │   P2   │  P4   │   P1   │    P3     │
        └────┴────────┴───────┴────────┴───────────┘
```

**Calculation:**

   Process    AT   BT   CT   TAT   WT   RT
  --------- ---- ---- ---- ----- ---- ----
     P1        0    8   17    17    9    0
     P2        1    4    5     4    0    0
     P3        2    9   26    24   15   15
     P4        3    5   10     7    2    2

-   **Average Waiting Time:**

    \[ AWT = `\frac{9+0+15+2}{4}`{=tex} = 6.50 \]

-   **Average Turnaround Time:**

    \[ ATAT = `\frac{17+4+24+7}{4}`{=tex} = 13.00 \]

-   **Average Response Time:**

    \[ ART = `\frac{0+0+15+2}{4}`{=tex} = 4.25 \]

### D. Priority Scheduling

Priority Scheduling allocates the CPU to the process with the highest
priority.

Since the assignment does not provide priority values, the following
values are assumed in ascending order:

   Process    Priority
  --------- ----------
     P1              1
     P2              2
     P3              3
     P4              4

Smaller priority number means higher priority.

**Execution order:** `P1 → P2 → P3 → P4`

**Gantt Chart:**

``` text
        0          8       12          21       26
        ┌──────────┬───────┬───────────┬────────┐
        │    P1    │  P2   │    P3     │   P4   │
        └──────────┴───────┴───────────┴────────┘
```

**Calculation:**

   Process    AT   BT   Priority   CT   TAT   WT   RT
  --------- ---- ---- ---------- ---- ----- ---- ----
     P1        0    8          1    8     8    0    0
     P2        1    4          2   12    11    7    7
     P3        2    9          3   21    19   10   10
     P4        3    5          4   26    23   18   18

-   **Average Waiting Time:**

    \[ AWT = `\frac{0+7+10+18}{4}`{=tex} = 8.75 \]

-   **Average Turnaround Time:**

    \[ ATAT = `\frac{8+11+19+23}{4}`{=tex} = 15.25 \]

-   **Average Response Time:**

    \[ ART = `\frac{0+7+10+18}{4}`{=tex} = 8.75 \]

### E. Round Robin (RR)

Round Robin is a preemptive scheduling algorithm in which every process
receives a fixed amount of CPU time called a time quantum.

When a process does not complete within its allocated time, it is moved
to the end of the ready queue.

**Given time quantum:** 3 units.

**Execution order:** `P1 → P2 → P3 → P4 → P1 → P2 → P3 → P4 → P1 → P3`

**Gantt Chart:**

``` text
        0    3    6    9    12   15   16   19   21  23  26
        ┌────┬────┬────┬────┬────┬────┬────┬────┬───┬───┐
        │ P1 │ P2 │ P3 │ P4 │ P1 │ P2 │ P3 │ P4 │P1 │P3 │
        └────┴────┴────┴────┴────┴────┴────┴────┴───┴───┘
```

**Calculation:**

   Process    AT   BT   CT   TAT   WT   RT
  --------- ---- ---- ---- ----- ---- ----
     P1        0    8   23    23   15    0
     P2        1    4   16    15   11    2
     P3        2    9   26    24   15    4
     P4        3    5   21    18   13    6

-   **Average Waiting Time:**

    \[ AWT = `\frac{15+11+15+13}{4}`{=tex} = 13.50 \]

-   **Average Turnaround Time:**

    \[ ATAT = `\frac{23+15+24+18}{4}`{=tex} = 20.00 \]

-   **Average Response Time:**

    \[ ART = `\frac{0+2+4+6}{4}`{=tex} = 3.00 \]

## 4. Comparison of All CPU Scheduling Algorithms

  Scheduling Algorithm     Average WT   Average TAT   Average RT
  ---------------------- ------------ ------------- ------------
  FCFS                           8.75         15.25         8.75
  SJF                            7.75         14.25         7.75
  SRTF                           6.50         13.00         4.25
  Priority                       8.75         15.25         8.75
  Round Robin (q = 3)           13.50         20.00         3.00

**Evaluation:**

1.  FCFS is simple to implement, but short processes may wait behind
    long processes.
2.  SJF reduces average waiting time but may cause starvation of long
    processes.
3.  SRTF provides the lowest average waiting and turnaround times for
    the given processes.
4.  Priority Scheduling executes higher-priority processes first, but
    low-priority processes may starve.
5.  Round Robin provides fair CPU allocation and better initial response
    time, but a small time quantum increases context-switching overhead.

## 5. Evaluation of Round Robin with Different Time Quanta

The assignment requires testing Round Robin with time quantum values of
1, 3, 5 and 20.

  --------------------------------------------------------------------------
    Time Quantum     Average WT    Average TAT     Average RT  Number of CPU
                                                                      Slices
  -------------- -------------- -------------- -------------- --------------
               1          12.50          19.00           0.75             26

               3          13.50          20.00           3.00             10

               5          11.00          17.50           5.50              6

              20           8.75          15.25           8.75              4
  --------------------------------------------------------------------------

**Evaluation:**

-   **Quantum 1:** Provides faster response but causes more scheduling
    operations.
-   **Quantum 3:** Provides a balance between response time and
    scheduling overhead.
-   **Quantum 5:** Reduces the number of scheduling operations and
    improves average waiting time for this example.
-   **Quantum 20:** Behaves like FCFS because all processes finish
    during their first CPU turn.

A small time quantum increases context-switching overhead. A large time
quantum reduces the benefits of Round Robin.

> **Note:** CPU slices are not the same as actual context-switch
> measurements. For these runs, the number of transitions between
> consecutive CPU slices is 25, 9, 5 and 3 respectively.

## 6. Starvation in SJF and Priority Scheduling

Starvation occurs when a process waits indefinitely because other
processes continuously receive CPU time before it.

-   In SJF, long processes may starve if short processes keep arriving.
-   In Priority Scheduling, low-priority processes may starve if
    high-priority processes continuously arrive.

### Ageing

Ageing is a technique used to prevent starvation by gradually increasing
the priority of processes that wait for a long time.

For example, when a process waits for a long period, its priority is
increased so that it eventually receives CPU time.

**Evaluation:** Ageing improves fairness and ensures that low-priority
processes also get an opportunity to execute.

## 7. Multilevel Feedback Queue (MLFQ)

Multilevel Feedback Queue is a CPU scheduling algorithm that uses
multiple ready queues with different priority levels and time quanta.

### Working

1.  New processes enter the highest-priority queue.
2.  Short processes may finish in the higher-priority queue.
3.  Processes that use their entire time quantum are moved to
    lower-priority queues.
4.  Long processes receive longer time quanta in lower-priority queues.
5.  Ageing or periodic priority boosting prevents starvation.

### Advantages

-   Short jobs receive faster CPU access.
-   Long jobs can complete without blocking short jobs.
-   Different time quanta improve scheduling flexibility.
-   Ageing reduces starvation.
-   Improves responsiveness and CPU utilisation.

**Evaluation:** MLFQ is suitable for video-encoding services because it
handles short and long jobs efficiently.



### Compile and Run

Save the program as `round_robin.c`.

``` bash
gcc round_robin.c -o round_robin
```

Run with the default time quantum of 3:

``` bash
./round_robin
```

Run with different time quantum values:

``` bash
./round_robin 1
./round_robin 3
./round_robin 5
./round_robin 20
```

## 9. Alternative Concept -- Multilevel Feedback Queue

If the cloud video-encoding service uses Multilevel Feedback Queue
instead of a single Round Robin queue, processes are distributed across
multiple queues according to their CPU usage.

### Comparison

  -----------------------------------------------------------------------
  Feature                 Round Robin             MLFQ
  ----------------------- ----------------------- -----------------------
  Number of queues        Single queue            Multiple queues

  Time quantum            Fixed                   Different for each
                                                  queue

  Short jobs              Wait for their turn     Usually finish in
                                                  higher queue

  Long jobs               Receive equal time      Move to lower queues
                          slices                  

  Starvation              Fair rotation among     Possible without ageing
                          ready processes         

  Flexibility             Less flexible           More flexible
  -----------------------------------------------------------------------

### Time Complexity Analysis

**Round Robin:**

-   Queue insertion: `O(1)`.
-   Queue deletion: `O(1)`.
-   Each process may execute multiple times.
-   For `n` processes and total CPU burst `B`, the scheduling simulation
    takes `O(B/q + n)` operations, where `q` is the time quantum.

**Multilevel Feedback Queue:**

-   Queue insertion: `O(1)`.
-   Queue deletion: `O(1)`.
-   Selecting the highest-priority non-empty queue can be `O(1)` when
    using a fixed number of queues.
-   Moving processes between queues also takes `O(1)` per operation.

### Space Complexity

-   **Round Robin:** `O(n)` for storing process and queue information.
-   **MLFQ:** `O(n)` for storing process information across multiple
    queues.

**Evaluation:** MLFQ provides better flexibility because it considers
process behaviour. However, it requires careful selection of queue
priorities, time quanta and ageing mechanisms.

## 10. Design Decision

For the cloud video-encoding service, I recommend **Multilevel Feedback
Queue Scheduling**.

### Reasons

1.  Short video jobs receive faster CPU access.
2.  Long encoding jobs can execute in lower-priority queues.
3.  Different time quanta improve scheduling flexibility.
4.  Ageing prevents starvation of long-running jobs.
5.  It balances responsiveness and CPU utilisation.

Although SRTF provides the lowest average waiting time for the given
four processes, MLFQ is more suitable for a real-world video service
where job lengths vary and fairness is important.

## 11. Conclusion

CPU scheduling plays an important role in improving system performance.
FCFS is simple, SJF and SRTF reduce average waiting time, Priority
Scheduling executes important processes first, and Round Robin provides
fair CPU allocation.

MLFQ is recommended for the cloud video-encoding service because it
handles short and long jobs efficiently while reducing starvation
through ageing.

------------------------------------------------------------------------

**End of Assignment**
