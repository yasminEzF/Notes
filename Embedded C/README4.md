# Embedded C Lec 4 (Pointers)

## Pointer to Pointer

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


