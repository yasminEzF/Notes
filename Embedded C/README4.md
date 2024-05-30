# Embedded C Lec 4 (Pointers)

## Pointer to Pointer (double-pointer)

```c
int x;
int *ptr;
int **pptr;

x = 30;
ptr = &x;
pptr = &ptr;
// x = *ptr = **pptr 
```

## Array of Pointers

```c
char *ptrArr[2] = {"ab","cd"};
```

## pointer to function

Used for `Callback Functions` that allow lower-layer modules to call upper-layer functions.

```c
int (*funPtr)(void);
int fun(void);

funPtr = fun;
(*funPtr)(); 
```

## complicated declarations

```c
int (*fp)();    // int funPtr (void)
int (*ptr)[13]; // ptr to array of 13 int

void (*f[10])(int,int)  // array of 10 ptr to fun returning void taking args (int, int)
(*f[0])(4,5);   //fun call

char (*(*x())[]) (); // x is fun returning pointer to array of ptrs to fun returning char taking void

char (*(*x[3])())[5];   // x is array of 3 ptr to fun taking void returning ptr to arr of 5 char

int *(*(*arr[5])()) (); // arr of 5 ptr to fun returning ptr to fun returning ptr to int

void (*bsd_signal(int sig, void (*func)(int)))(int);    // bsd_signal is a fun taking (int, ptr to fun taking int returning void) as arguments returning ptr to fun taking int and returning void
```

```c
char foo;   //var
char* foo;  //ptr to char
char foo[5];    //arr of 5 chars
char* foo[5];   // arr of 5 ptr to char
char (*foo)[5]; // ptr to arr of 5 char
char* foo(char*);    //fun taking ptr to char returning ptr to char    
char* (*foo)(char*);    // ptr to fun taking ptr to char returning ptr to char
char* (*foo[5])(char*);     // arr of 5 ptr to fun
char* (*(*foo)[5])(char**); // ptr to arr of 5 ptr to fun
```

## constant pointers

```c
const int *ptr;     // ptr to const int (can't modify data)
int const *ptr;

int* const ptr;     // const ptr to int (can't move ptr)

const int * const ptr;  // const ptr to const int (can't move or modify)
int const * const ptr;
```

```c
#include <stdio.h> 

const int a = 10; /* Global const stored in .rodata in ROM */

int main() {
  const int b = 5;  /* Local const stored in stack */
  int *ptr = &b;    

  *ptr = 20;        /* Modified */
  
  printf("b = %d\n",b);     // b = 20
  
  ptr = &a;         
  *ptr = 20;        /* Segmentation fault (can't write flash) */
    
  printf("a = %d\n",a);
    
  return 0;
}
```

## Pointer Types

### Generic pointer

points to unknown type, can be casted to any for use.

```c
void* ptr;
```

### NULL pointer

NULL pointers do not point to any memory location, can't be dereferenced.

```c
int* ptr = NULL;
```

### Dangling pointer

A pointer pointing to memory that has been freed which results in Undefined behavior if dereferenced.

```c
int* ptr = (int*)malloc(sizeof(int));
free(ptr);
*ptr = 10; 
```

```c
int* getPointer() {
    int x = 10;
    return &x; // Local variable x goes out of scope
}
int* ptr = getPointer(); //ptr points to out of scope memory address
```

### Wild pointer

A pointer that has not been initialized to a known valid memory location which results in Undefined behavior if dereferenced.

```c
int* ptr;   
*ptr = 10;  // not initialized
```

```c
int* ptr;
free(ptr);
*ptr = 10; // memory deallocated
```

### Near, Far, Huge pointers

idk, skipped.

## Dynamic memory allocation

from `stdlib.h`

- `malloc`: returns void pointer to allocated memory, returns `NULL` if failed to allocate.
- `calloc`: allocates and initializes by zero.
- `free`: de-allocates memory.
- `realloc`: maintains data while reallocating a new block.

`memory leak` occurs when allocating memory in heap and not deleting it.

```c
int* ptr1 = (int*)malloc(5*sizeof(int));

int* ptr2 = (int*)calloc(5, sizeof(int));

free(ptr1);

ptr2 = (int*)realloc(ptr2, 10*sizeof(int));
```

## Coding guidelines
