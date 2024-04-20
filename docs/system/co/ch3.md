# Arthimetic for Computer

## Intrduction

- Computer words are composed of bits
    - thus words can be represented as binary numbers
    - there are 32bit/word or 64bits/word in RISC-V
    - Contains four bytes or eight bytes
- Generic Implementation
    - use the PC to supply instruction address
    - get the insturction from memory
    - read registers
    - use the instruction to decide exactly what to do

## Arithmetic

### Multiplication

![](image/3.3.png)

对于 $n$ bit 乘法，首先将乘数放到积寄存器的低位，在每轮迭代中检测积的第 0 位是否为 1，如果是则将积的高位加上被乘数后右移，否则仅做右移操作，迭代 $n$ 轮。

!!! Example

    ![](image/3.4.png)


#### Booth's Algorithm

![](image/3.5.png)

![](image/3.6.png)

注意移位的时候要保证符号位不变(算术右移)

!!! Example 

    ![](image/3.7.png)

### Division

![](image/3.8.png)

!!! Example 

    ![](image/3.9.png)


## Floating point numbers

![](image/3.10.png)

### IEEE 754 standard

- Leading 1 bit of significand is implicit(saves one bit)
- Exponent is biased: (127 for single precision, 1023 for double precision)

$$
(-1)^{sign}\cdot (1+significand) \cdot 2^{exponent - bias}
$$

#### Single-Precision Range

![](image/3.11.png)

#### Double-Precision Range

![](image/3.12.png)

![](image/3.13.png)

#### Infinities and NaNs

![](image/3.14.png)

### Floating point addition

- Alignment
- The proper digits have to be added
- Addition of siginificands
- Normalisation of the result
- Rounding

![](image/3.16.png)


!!! Example 

    ![](image/3.15.png)


