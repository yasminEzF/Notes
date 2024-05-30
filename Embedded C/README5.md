# Embedded C Lec 5

## Sign Qualifiers

By default, declared variables are `signed` unless explicitly declared `unsigned`.

![alt text](image-12.png)

`Signed magnitude` representation resulted in some drawbacks

![alt text](image-13.png)

`One's compliment` representation solved the issue for mathematical operations but still had it's drawback of having `+0` and `-0`.

![alt text](image-14.png)

`Two's compliment` representation solved both issues, where a number's 2's compliment is its `1's compliment + 1`.

![alt text](image-15.png)

-128 | 0 | 127
---------|----------|---------
 0b1111 1111 | 0b0000 0000 | 0b0111 1111

Assigning a value greater than variable range will result in `overflow` while assigning a value less than range will result in `underflow`.

```c
char a = 128;   // a = -128
char a = -129;  // a = 127
unsigned char a = -1;   // a = 255
```

## Float vs Double

I don't understand it but `sizeof(double)` > `sizeof(float)`

## Size qualifiers

Data type sizes vary according to compiler

Data Type | min size
---------|----------
`char`| 1-byte
`short int`| 2-bytes
`long int`| 4-bytes
`float`| 4-bytes
`double`| 8-bytes
`long double`| 12-bytes

An example of varying data sizes would look like this:

Compiler | `char` | `int` | `long int`
---------|----------|---------|---------
Compiler 1 | 1-byte | 2-bytes | 4-bytes
Compiler 2 | 2-bytes | 4-bytes | 8-bytes

A way of overcoming this would be by creating a `standardTypes.h` with defines for each type.

```c
#define uint8_t     unsigned char
#define sint8_t     signed char
```

## Typedef

`typedef` is used to give alias for existing data types or user-defined ones.

```c
typedef unsigned char uint8_t;
typedef struct{
    int x;
}X_struct;
```

Generating `standardTypes.h` can be done with `typedef` or `#define` with the difference of `typedef` is resolved during compilation while `#define` is resolved during preprocessing.

```c
#define ptrHash int*
typedef int* ptrDef

int main(){
    ptrHash ptr1, ptr2;     // ptr1 (ptr to int), ptr2 (int)
    ptrDef ptr3, ptr4;      // ptr3 (ptr to int), ptr4 (ptr to int)
}
```

## Header Guard

Assures header file inclusion only once and so `macros`, `typedefs`, `enums` and `function prototypes` exist once and avoid `multiple definitions error`.

```c
#ifndef HEADER_H_
#define HEADER_H_

#endif
```

## Memory Layout in C

![alt text](image-16.png)

- `text segment`: contains executable code and program logic, read-only.
- `heap`: dynamically allocated memory.
- `data`:
  - `initialized`: global initialized and static variables with non-zero values.
    - `initialized read-only`
    - `initialized read-write`
  - `non-initialized (bss)`: uninitialized global and static variables that get initialized with zero values either implicitly if not initialized or explicitly.
- `stack`: local variables and stack frames (return address, function arguments, local variables,...) for function calls.
