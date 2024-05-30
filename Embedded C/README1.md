# Embedded C Lec 1

## Bit fields

```c
struct reg{
    unsigned int x : 2;
    unsigned int : 1;       //no name for reserved
    unsigned int y : 5;
}
```

### features

- unnamed fields are not accessible
- reserves size for **1 int** until size is exceeded so it reserves another (even if not all of it is used)
- if a different type, a new space of that type will be reserved
- works with `int`, `char`, `short` but not `float`

### restrictions

- can't create arrays
- can't get address of each member (bit)
- not portable and depends on endianness
- can't use `sizeof()` with a user defined type (will result in compilation error)

## Aligned vs unaligned data access

## Padding vs Packing

![image](https://github.com/yasminEzF/Notes/assets/109252157/b65ef71d-8d12-4906-8b09-fc9a88f30deb)

|aligned (padding) | unaligned (packing)
|---------|----------
| 1 instruction for element access | could need more than 1 instruction to access a single element
| efficient code | more instructions are generated
| less code size | instructions are generated for each byte
| memory waste (padding) | no memory waste

- padding is compiler default, in order to overwrite it use `#pragma pack(n)` where `n` is number of bytes to align to.

## Enum

```c
enum bool{
    FALSE = 0,
    TRUE
};

enum bool x;
```

`enums` take up memory size equivalent of `int`

## Union

```c
union packet{
    unsigned char data[16];
    struct{
        unsigned int header;
        unsigned int payload;
    }fields;
};
```

`union` share the same memory and take up space equivalent to the largest element.

## Little endian vs Big endian

## Coding Guidlines

### MISRA C

- no dynamic allocation
- no multiple exit points in a function (single return)
