# Lecture 1

## 

Memory is a 2-way interface with `uP`, where `instructions` and `data` are stored. Software is written to memory before runtime.

![alt text](image.png)

Info provided by processor manufacturer:

- Instruction Set Architecture
- First execution address
- step between instructions execution

Instruction Set is a readable way of `uP` execution, but actual instructions in memory are written in binary.

## Instruction Set Architecture

Instructions work with registers

![alt text](image-1.png)

Instruction:    `opCode` `Register1` `Register2`


|`Memory` | `Registers`
|---------|----------
| Accessed through address | Accessed through index

![alt text](image-2.png)

![alt text](image-4.png)

![alt text](image-3.png)

Control unit routes to Fetch unit, Decode unit, ALU

Fetch unit is responsible for load and store, it also fetches instruction at PC address and increments PC

ALU is used to execute arithmetic and logic only not executing instructions itself

