# Embedded C Lec 6

## storage classes

|storage specifier | storage | init value | scope | lifetime
|---------|----------|---------|---------|----------
| auto | stack | garbage | local to block | block
| extern | data | zero | global multi-file | program lifetime
| static | data | zero | local to block | program lifetime
| register | cpu | garbage | local to block | block

can't use more than one storage class for same variable.

### auto

Automatic variables are allocated memory automatically at runtime which is the default for all variables.

### static

stored in data memory and initialized once, maintains value between function calls.

### extern

variable is defined somewhere else in the program and will be resolved at linking, extern variables are not allocated memory (declaration not definition). initialized once globally. having gloabl variables of same name in diff files is not allowed (multiple definitions)

### register

allocated in CPU registers depending on availablitiy in CPU, access time is faster, can't dereference (&register NO), only suggestion but ultimately compiler decides, can store pointer in reg since reg can contain variable address

## Optimization

- reducing number of instructions (code space optimization)
- reducing memory access time (time space optimization)
- reducing power consumption

```c
int main(){
    uint32_t a;
    uint32_t b;
    a = 1;
    b = a;
    while(1){

    }
    return 0;
}
```

### -O0

- no optimization done
- easier to debug
- fastest compilation time
- not guranteed to work correctly if compiled with a different optimization level
- one-to-one mapping between generated instructions and code

```c
int main(){
    uint32_t a;
    uint32_t b;
    a = 1;
    b = a;
    while(1){

    }
    return 0;
}
```

### -O1

- no speed-space tradeoffs
- smaller and faster executable than `-O0`

### -O2

- slower compilation
- applies optimization algorithms on code
- hard to debug
- no longer one-to-one mapping between generated instructions and code

### -O3

- slowest compile time
- aggressive optimization
- can cause bugs

```c
int main(){ 
    return 0;
}
```

## Type  qualifiers

### `const`

### `volatile`

value may change during runtime and not by code so it is readed every time from memory address and no optimization is performed related to it. `volatile` is mainly used with:

- memory-mapped registers
- global variables (modified by ISR)
- global variables (multi-threaded app)

```c
volatile int x;         //volatile var
int volatile x;         //volatile var
volatile int* ptr;      //ptr to volatile int
int volatile* ptr;      //ptr to volatile int
int* volatile ptr2;     //volatile ptr to regular int
int volatile * volatile ptr3;   //volatile ptr to volatile int
```

```c
while(register == 0){
/* in case register is not volatile, compiler assumes condition to be true forever and doesn't check condition */
/* using volatile disables this optimization and the condition is checked for every loop */
}
```

## Interrupt Service Routine (ISR)

ISR's are special functions that execute based on an event that occurs during runtime, so implementing them as regular functions would cause the compiler to remove them if optimization is set (ex: if(var == 1) which would only be set through an event during runtime).

## Multi-threaded apps

Two tasks can exchange data through shared memory. Same problem can occur from optimization since compiler is not aware that a different task will modify the shared variable. To solve such issue, variable will be declared as `volatile`.

## Toolchain

- assembler
- linker
- debugger
- compiler
- libraries
- binary utilities (binutils): `objdump`, `objcopy`, `size`, ...

- build m/c: toolchain built.
- host m/c: toolchain run & generated executable.
- target m/c: executable generated for it.

- native toolchain
- cross-compilation toolchain

## Build process

![alt text](image-10.png)

### preprocessing

- remove comments
- expand macros
- expand includes
- conditional compilation

### compilation

translate C code to assembly instructions

### assembler

translate assembly instructions to machine-level instructions and produce object files (relocatable files)

### linking

creates a final executable file and `.map` file.

`.map` file contains lengths of the sections and the names and locations of the public symbols found in the executable file.

![alt text](image-11.png)
