# Build Process

![alt text](image-8.png)

All errors are either compilation errors if object files couldn't be produced, or linking errors if object files were generated but final executable could not.

## Preprocessor

Remember that preprocessor as a tool doesn't generate error, but pass them along to compiler and then compiler generates compilation errors.

Consists of 4 stages:

### 1. character set

- checks file encoding matches its own.
- Can open, read & parse characters (Ex: End of line character (`\n`)).

`file encoding` is the way of encoding a character into a binary (~~not sure~~).

### 2. initial processing

1. read file.
    - written in RAM line by line by detecting `\n` character.
2. process Trigraphs (if exist).
   - old way of writing special characters due to keyboard character limitations (Ex: `??(` = `[`).
   - needs to be enabled as compiler option.
3. remove `\` and replace with next line.
   - such as in Macro-like function when trying to replace with multiline text.
4. remove comments and replace with a single space character.

```c
/*      line replacement    */
#define x(args)     do{ \
                    args++;\
                    }while(true)

uint\
3\
2\
 x = 30\
00;             // will be uint32 x = 3000;     (be mindful of spaces)

/*      comment replacement     */
// single line -> gets replaced :(

/*
*   multiline   ->  gets replaced :(
*/

// comment /*
errorrrrr
*/

// comment  \
error\
wtf
```

### 3. tokenization

Preprocessor divides each line into tokens for compiler to understand, tokens are greedy.

```c
int a = b + c;  // T1 (int), T2 (a), T3 (=), T4 (b), T5 (+), T6 (c), T7 (;)

a = z;  // T1 (a), T2 (=), T3 (z), T4 (;)

a+++++b; // (((a++)++)+b)   GREEDYYYYYY
```

![alt text](image-9.png)

### 4. preprocessor language

using preprocessor directive `#`

1. file inclusion   `#include<stdlib.h>` or `#include"usrLib.h"`
2. line control     `#line fileName n-line`
   - used for code generation/injection
3. macro expansion  `#define MACRO  replacement`
   - seraches for Token and replace
4. code inclusion/exclusion `#if #elif #ifndef #ifdef #endif`
5. diagnostics      `#warning #error`

`#pragma` is a compiler directive not preprocessor.

```c
#define x   10ul                    //unsigned long
#define y   (unsigned long)10       //cast
```

## Compiler

### Front End

- is language specific (only layer that understands actual c-language)
- checks C for compilation
- **Output:** abstract synatx file (Ex: Abstract Syntax Tree (AST))

### Middle End

- is global optimizer
- remove not used variables, dead code, reorder conditions,...
- **Output:** optimized abstract synatx file

### Back End

- machine specific
- **Output:** assembly file with machine specific instructions

## Assembler

- translates assembly instructions into machine code
- **Output:** objectFile (including symbol table in each file)
- object files addressing is logical not physical, starting from 0x00.

## Linker

`elf`: executable linkable format
