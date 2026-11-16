Online Examination Server – Process Management

1. Problem Understanding

An online examination server handles 2,000 students simultaneously. Each student has a separate examination session, while other processes handle automatic saving and video proctoring.

When too many processes execute simultaneously, the server may become slow due to:

- Context Switching: CPU switches between processes frequently, increasing overhead.

- Memory Consumption: Each process requires memory for its execution and management.

- I/O Waiting Time: Processes may wait for database, disk or network operations.

- CPU Scheduling Overhead: The operating system spends more time scheduling processes.

2. Process-State Diagram


  Explanation of process states:

- New: Process is being created.

- Ready: Process is waiting for CPU allocation.

- Running: Process is currently executing.

- Waiting: Process is waiting for I/O or an event.

- Terminated: Process has completed execution.

Transitions:

1. New → Ready: Process is admitted.

2. Ready → Running: CPU scheduler selects the process.

3. Running → Ready: Time quantum expires or process is preempted.

4. Running → Waiting: Process requests I/O.

5. Waiting → Ready: I/O operation completes.

6. Running → Terminated: Process finishes execution.

Points below the diagram:

- A process moves between different states during its execution.

- Only a running process can execute instructions using the CPU.

- The operating system manages these state transitions.

- Proper scheduling helps improve CPU utilisation and system performance.

3. Process Control Block (PCB)

- Process Identification: Stores the unique Process ID.
- Process State: Stores the current state of the process.
- Program Counter: Stores the address of the next instruction.
- CPU Registers: Stores CPU register values.
- CPU Scheduling Information: Stores priority and scheduling details.
- Memory Management Information: Stores memory allocation details.
- I/O Status Information: Stores allocated I/O devices.
- Accounting Information: Stores CPU usage and execution time.


A Process Control Block (PCB) is a data structure maintained by the operating system to store information about a process.

Components of PCB:

- Process ID: Unique process identification.

- Process State: Current state of the process.

- Program Counter: Address of the next instruction.

- CPU Registers: Stores register values.

- Scheduling Information: Priority and scheduling details.

- Memory Information: Memory allocation details.

- I/O Information: Details of allocated I/O devices.

- Accounting Information: CPU usage and execution time.

Points below the diagram:

- Every process has its own PCB.

- The operating system creates a PCB when a process is created.

- PCB helps the operating system manage and track processes.

- During context switching, process information is saved and restored using the PCB.

- The PCB is removed after the process terminates.


Points to remember:

- fork() creates a new child process.

- getpid() returns the current process ID.

- getppid() returns the parent process ID.

- wait() makes the parent wait for child processes to complete.

- The program creates three child processes.

Linux commands to execute:

gcc process.c -o process

./process


To observe processes:

ps

ps -f

pstree


5. Evaluation of 2,000 Processes

- Context Switching: More processes increase scheduling and switching overhead.

- Memory Consumption: Each process requires its own process-management structures and address space.

- I/O Waiting: Processes waiting for network, database or disk operations cannot use the CPU.

- CPU Utilisation: Excessive switching can reduce useful CPU execution time.

- Scalability: Creating a separate process for every student can become expensive.

6. Design Decision

One process per student:

- Advantages:

  - Strong isolation.

  - Better fault protection.

- Disadvantages:

  - High memory consumption.

  - Increased process creation overhead.

One thread per student:

- Advantages:

  - Lower memory usage.

  - Faster communication.

- Disadvantages:

  - Shared-memory errors.

  - Weaker fault isolation.

Fixed worker-process pool:

- Advantages:

  - Controlled resource usage.

  - Better scalability.

  - Reduced process creation overhead.

- Disadvantages:

  - Requires job distribution.

  - Needs load balancing.

Recommended Design: Fixed Worker-Process Pool

A fixed pool of worker processes is recommended because it limits the number of active processes, reduces memory consumption and avoids creating 2,000 separate processes simultaneously.

The server can distribute examination requests among available workers.

7. Alternative Concept – Threads

If each student session uses a thread instead of a separate process:

- Threads share code, data and open files.

- Each thread has its own stack, registers and program counter.

- Thread creation and context switching are generally less expensive.

- Threads can communicate through shared variables.

- A faulty thread can affect the entire server process if it causes a process-level failure.

Memory comparison:

- Process: Separate virtual address space and process-management structures.

- Thread: Shares the process address space and has its own stack and thread-management information.

8. Conclusion

A separate process for every student provides isolation but increases memory usage and context-switching overhead. A fixed worker-process pool is a better choice for the online examination server because it provides controlled resource allocation, scalability and efficient CPU utilisation.



































