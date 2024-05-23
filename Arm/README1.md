# Lecture 1

## 

Memory is a 2-way interface with `uP`, where `instructions` and `data` are stored. Software is written to memory before runtime.

![image](https://github.com/yasminEzF/Notes/assets/109252157/bacc921a-6e43-498b-ac78-54e29255444a)

Info provided by processor manufacturer:

- Instruction Set Architecture
- First execution address
- step between instructions execution

Instruction Set is a readable way of `uP` execution, but actual instructions in memory are written in binary.

## Instruction Set Architecture

Instructions work with registers

![image-1](https://github.com/yasminEzF/Notes/assets/109252157/19f1ae83-f932-49f1-a10a-0c8d47f1cf36)

Instruction:    `opCode` `Register1` `Register2`


|`Memory` | `Registers`
|---------|----------
| Accessed through address | Accessed through index

![image-2](https://github.com/yasminEzF/Notes/assets/109252157/155a6d3d-9b29-495c-91f3-03b508d37d9b)

![image-4](https://github.com/yasminEzF/Notes/assets/109252157/1cae34cb-f827-48e3-9f5b-5c60e853de45)

![image-3](https://github.com/yasminEzF/Notes/assets/109252157/d747854b-f930-4fe7-ade8-eb50a778c6f6)

Control unit routes to Fetch unit, Decode unit, ALU

Fetch unit is responsible for load and store, it also fetches instruction at PC address and increments PC

ALU is used to execute arithmetic and logic only not executing instructions itself

