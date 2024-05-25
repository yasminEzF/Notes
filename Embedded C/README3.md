# Embedded C Lec 3

## type conversion in C

- explicit: large to small
- implicit: small to large

implicit | explicit
---------|----------
 by compiler | by user
 smaller to larger | larger to smaller
 no data loss | could cause data loss

## type qualifiers

### `const`

```c
const int x = 5;
int const y = 7;
```

### `volatile`

## pointers

Pointer Size is Equivalent to memory Location address Size (data bus)

### pointer arithmetics

```c
ptr++;
ptr--;
```

Every Time a Pointer is incremented, it Points To The Next Memory Location of its Base Type.

![image-3](https://github.com/yasminEzF/Notes/assets/109252157/b164e500-6a05-4196-88c0-385c10693a6e)

### pointer to array

```c
str[4] = *(ptr + 4)
&str[4] = (ptr + 4)
```

### pointers and strings

### array of pointers

```c
char *p[4];
char y = *p[1];
```

### pointer to structure

```c
ptr -> member;
```
