# Inter-Process Communication (IPC) Using Pipes and Shared Memory

## 1. Aim

To implement Inter-Process Communication (IPC) between two processes
using pipes and shared memory, and compare their data-transfer
performance.

## 2. Theory

Inter-Process Communication (IPC) allows two or more processes to
exchange data and coordinate their activities.

### A. Pipe

-   A pipe is a communication channel between processes.
-   One process writes data into the pipe.
-   Another process reads the data from the pipe.
-   The operating system manages the pipe buffer.
-   When the writing process closes the write end, the reader receives
    EOF (End of File).

**How does a pipe signal the end of data?**

When the parent process closes the write end of the pipe, the child
process's `read()` function returns `0` after all buffered data has been
read. This indicates that no more data is available.

If the write end is not closed, the reader may wait indefinitely for
more data.

### B. Shared Memory

-   Shared memory allows multiple processes to access the same memory
    region.
-   Processes can exchange data without repeatedly copying it through a
    kernel-managed pipe buffer.
-   It is generally faster for large data transfers.
-   Synchronisation mechanisms such as semaphores or mutexes are
    required to prevent simultaneous access problems.

### C. Difference Between Pipe and Shared Memory

  -----------------------------------------------------------------------
  Feature                 Pipe                    Shared Memory
  ----------------------- ----------------------- -----------------------
  Communication           Kernel-managed buffer   Common memory region

  Data copying            Data passes through     Processes access the
                          kernel buffer           shared region directly

  Speed                   Comparatively slower    Usually faster for
                                                  large data

  Synchronisation         Pipe manages data flow  Explicit
                                                  synchronisation
                                                  required

  Complexity              Simple                  More complex
  -----------------------------------------------------------------------



## 4. Performance Comparison

The assignment asks you to compare data transfer for **1 MB, 10 MB and
100 MB** using both methods.

> **Important:** Do not enter assumed execution times. Run the programs
> or a transfer benchmark for each size and record the actual time
> measured on your system.

  Data Size   Pipe Execution Time     Shared Memory Execution Time
  ----------- ----------------------- ------------------------------
  1 MB        Record measured value   Record measured value
  10 MB       Record measured value   Record measured value
  100 MB      Record measured value   Record measured value

### Expected Observation

-   Pipe communication involves transferring data through a
    kernel-managed buffer.
-   Shared memory avoids repeated kernel-buffer transfers after the
    shared region is established.
-   Shared memory can be faster for large data transfers.
-   Shared memory needs explicit synchronisation, while pipes manage the
    data flow.

## 5. Alternative Solution -- Message Queue

Another IPC method is a Message Queue.

-   A message queue allows processes to exchange messages through an
    operating-system-managed queue.
-   The sender places messages into the queue.
-   The receiver retrieves messages from the queue.
-   It supports structured message-based communication.
-   It involves kernel-managed data transfer.

  Method          Advantage              Limitation
  --------------- ---------------------- --------------------------
  Pipe            Simple communication   Kernel-buffer copying
  Shared Memory   Fast for large data    Requires synchronisation
  Message Queue   Structured messages    Kernel overhead

## 6. Time and Space Complexity

For transferring `n` bytes:

-   **Pipe:** Transfer work is (O(n)).
-   **Shared Memory:** Writing and reading the data takes (O(n)).
-   **Message Queue:** Processing `n` bytes of messages is also
    proportional to the amount of data transferred.

Shared memory requires additional memory for the shared region. Pipes
and message queues use operating-system-managed buffers.

## 7. Conclusion

Inter-Process Communication allows independent processes to exchange
information. Pipes provide simple communication through a kernel-managed
buffer, while shared memory allows processes to access a common memory
region. Shared memory is suitable for large data transfers, provided
proper synchronisation is used.

