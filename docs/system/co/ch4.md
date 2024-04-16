# Processor Design

## Introduction

- CPU performance factors
    - 指令数量：由 ISA 和编译器决定
    - CPI and Cycle Time: Determined by CPU hardware

### Instruction Execution Overview

- For every instruction, the first two step are identical
    - Fetch the instruction from the memory
    - Decode and read the registers
- Next steps depend on the instruction class
    - Memory reference
    - Arithmetic logical
    - branches
- Depending on instruction class
    - Use ALU to calculate: arithmetic result, memory address for load/store, branch comparison
    - Access data memory for load/store
    - PC <- target address or PC + 4

![](image/4.1.png)

回顾在计逻中介绍的，我们不能直接将两根线连在一起，这时候我们就需要多路选择器来控制 CPU 的传输，综合起来就是一个控制单元。

![](image/4.2.png)

