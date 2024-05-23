# Notes (Lec 10)

## Notes

### Argument checking

When passing pointers as arguments, first thing to do is check that they're not `NULL` pointers.

For values checking, min & max values must be validated. In case of strings, string length must be validated too.

When having multiple options of the same value for different functionalities or functions, injecting an id to check for the correct option for its intended use or function.

```c
/* regular options */
#define FUN1_OPTION1         0x01
#define FUN1_OPTION2         0x02
#define FUN2_OPTION1         0x01
#define FUN2_OPTION2         0x02
/* injected id for function */
#define FUN1_OPTION1         0x11
#define FUN1_OPTION2         0x12
#define FUN2_OPTION1         0x21
#define FUN2_OPTION2         0x22
```

Same could be achieved by using 64-bit masks and accessing them through pointer to 32-bit variable, so that the first byte check is the value then increment the ptr to check id.

### Endianness

```c
uint32_t var = 0x12345678;
uint32_t *ptr = &var;
uint16_t *ptr16 = (uint16_t*)ptr;
uint8_t *ptr8 = (uint8_t*)ptr;
```

![alt text](image-5.png)

Column A | Little endian | Big endian
---------|----------|---------
 *ptr | 0x12345678 | 0x12345678
 *ptr16 | 0x5678 | 0x1234
 *ptr8 | 0x78 | 0x12

### Optimization

Code optimization works on one parameter while using more of the other, for example optimizing speed for faster code execution, more memory will be consumed (storing all possible values from an equation in an array to save calculation time) or optimizing memory usage while losing execution speed (taking time to perform the calculation).

### Clock Control

- `HAL_sysclk` module responsible for system clk control
- `LED_init` responsible for GPIO enable

## Unit Testing



