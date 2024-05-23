# Embedded C Lec 2

## preprocessor directives

Used for compile time settings and text replacement. They appear after the first stage (pre-compilation) where source `file.c` outputs `file.i` as intermediate before compilation.

### Directives (File inclusion)

```c
#include <built-In_Lib.h>
#include "path/to/local_Lib.h"
```

### Macros

Used for configurability, readability, portability (int size variation?) and no memory usage.

```c
/* Object like macro */
#define object      value
/* Function like macro */
#define function(parameters)    replacement
```

Object-like

```c
#define x   10
int main(){
    int y = x;          //int y = 10;
    int xy = 20;
    int X = 50;
    printf("x = %d",x); //printf("x = %d",10);
    return 0;
}
```

Function-like

```c
#define print_info()        do{printf("Yass");\
                            printf("mesa mesa");\
                            printf("smth else")}while(0) //no semicolon at end ;

int main(){
    print_info();   // semicolon ; is used for the last statement
    /* the semicolon trick is mainly for easier coding */
    if(x == 1)
        print_info();   //will take first statement only in condition
    /* 
    * which is why it should be enclosed in curly brackets 
    * adding the last bracket ruins the last semicolon trick
    * solution would be to use a do while(0) with the semicolon to 
    * complete the last while
    */
    return 0;
}
```

![alt text](image-2.png)

```c
#define new_print(name)         printf(#name)   //stringification
#define CONCAT(b3,b2,b1,b0)     0b##b3##b2##b1##b0  //concat but needs helper function
#define helper(b3,b2,b1,b0)     CONCAT(b3,b2,b1,b0)
```

```c
#define x   3
#undef x
#define x   5
```

```c
/* predefined macros */
__STDC__                //
__STDC_VERSION__        //different value acc to C std
__STDC_HOSTED__         //indicates OS i think?
__FILE__                //name of file
__LINE__                //line number
__DATE__                //date of translation?
__TIME__                //time of translation? asctime()
```

### Conditional compilation

```c
#if
#elif
#else
#endif
#ifdef
#ifndef
```

### #Error

```c
#warning"message"
#error"message"
```

### #Pragma

It is compiler specific. If it is not supported, compiler simply ignores it.

```c
#pragma startup[priority] func   //priority is 64~255 for user
/* void __attribute__((constructor)) func(); */
#pragma exit[priority] func      //priority is 0~63 for c lib
                                 //default is 100
/* void __attribute__((destructor)) func(); */
#pragma once                //replaced by header guard
#pragma pack(n)             // n is (1,2,4,8) for alignment
```

## Definition vs Declaration

### Declaration

existence and type of a variable.

```c
extern int x;
```

### Definition

existence and type of a variable and also memory allocation for variable (optional initial value).

Declaration can happen multiple times in different parts but definition happens once only in a single source file (multiple definitions and linking errors).
