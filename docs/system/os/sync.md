# Synchoronization

- Processes/threads can execute concurently
- Concurrent access to shared data may result in data inconsistency.

## Race condition

- Several processes (or threads) access and manipulate the same data **concureently** and the outcome of the execution depends on the **particular order** in which the access takes place, is called a race-condition.
    - 在 kernel 里可能有多个 thread 同时去拿一个 thread_id （两个进程同时 fork, 子进程可能拿到一样的 pid）
- General structure of process
- critical section: (a critical sectioin segment of code, e.g. to change common variables, update table, write files etc.)
    - only one process in critical section, 
        - when one process in critical section, no other may be in its critical section.
        - each process must ask permission to enter critica section in entry section
        - the permission should be released in exit section
- critical section handling
    - single-core system: preventing interupts
    - multiple-processor: preventing interrupts are not feasible.Two approaches depending on if kernel is preemptive or non-preemptive.
        - preemptive: allows preemption of process when running in kernel mode.
        - non-preemptive: runs until exits kernel mode, blocks, or voluntraily yields CPU
    - **three requirements**
        - mutual exclusion (互斥访问): only one process can be execute in the critical section.
        - progress: 当没有线程在执行 critical section code 时，必须在申请进入临界区的线程中选择一个线程允许其执行临界区代码，保证程序执行的进展。
        - bounded waiting: 当一个进程申请进入临界区后，必须在有限的时间内获得许可并进入临界区，不能无限等待。

## Peterson's Solution

Peterson's solution solves two-processes/threads synchronizatioin (only works for two processes case).

- it assumes that LOAD and STORE are atomic(execution cannot be interrupted)
- Two processes share two variables
    - `boolean flag[2]`: whether a process is ready to enter the critical section.
    - `int turn`: whose turn it is to enter the critical secion.

可以验证符合 3 个 requirements.

Peterson's Solution 的问题：

- only works for two processes case
- it assumes that LOAD and STORE are atomic
- instruction order(指令可能会乱序，编译器调整 etc.)

## Hardware Support for Synchronization

Many systems provide hardware support for critical section code.


### Memory Barriers

Memory model are the memory guarantees a computer architecture makes to application programs.

- Strongly ordered: where a memory modification of one processsor is immediately visible to all other processors. (一个内存的修改会立刻被所有的 processor 看到)
- Weakly ordered: where a memory modification of one processor may not be immediately visible to all other processors.

A memory barrier is an instruction that forces any change in memory to be propagated (made visible) to all other processors.

### Hardware Instructions

#### Test-and-Set Instruction

Defined as below, but atomically.

```c
bool test_set(bool *target) {
    bool rv = *target;
    *target = TRUE;
    return rv;
}
```

example: 

```c
bool lock = FALSE;
do {
    while(test_set(&lock)); //busy wait
    critical section
    lock = FALSE；
    remainder section
}while(TRUE);
```