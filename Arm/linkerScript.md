# Linker Script (Lec 11)

![alt text](image-9.png)

## String handling in memory

```c
char* sptr = "my string";
```

when assigning a string like this, the string is created and stored in ROM and `sptr` contains its address. Since it gets stored in ROM, it is impossible to modify it during runtime and `sptr` is used as `ptr to const char`. A way to overcome this can be by creating a copy of it in RAM to write and modify.

![alt text](image-10.png)

## Linker Script

![alt text](image-11.png)

Up to the `file.o` stage, no data is included about target memory or physical addresses (logical addressing for each file independently). The `Linker` as a tool is generic for all architectures and what differentiates linking between archs is the `linker script` which works as a config file containing data about target memory size and physical addresses.

Compiler adds flags for Linker, changing compiler or linker needs reconfiguration for linker syntax.

![alt text](image-12.png)

As a concept, `Linker script` or `linker directive (ld)` contains the description of

- memory
  - name
  - start address
  - size
  - attributes (read, write, execute)
- sections
  - name
  - content
  - target memory
  - attributes (~~alignment~~, ...)

```c
memory{
    ROM(rx) : origin 0x0800 0000, length = 0x1000;
    RAM(rwx): origin 0x2000 0000, length = 1KB;
    myMemory(rw): origin 0x0000 0000, length = 0;
}
sections{
    .mySection{     
        (*.bss)     // first variable will be at RAM origin 
        (*.bss.*)      
        /* scan all object files and extract any section.bss or any section.bss.smth and add here */
    } > RAM //place section in RAM (will be placed first)

    .bss{   // .bss section start address will depend on .mySection size
        (*.*.bss)
    } > RAM
}
```

## **.**

Linker creates a dot `.` for each memory which contains current location and increments with each append.

![alt text](image-13.png)

## Linker API's

- `AddR(.mySection)` gets physical address of `mySection`.
- in a section, `label = .` adds a label to current location.

Being able to create `labels` in `linker script` must mean the ability to use them, but how do they get linked to code (startup code)? `Compiler` produces `file.o` with the variable as `UND` (undefined reference) in `symbol table` since it is written as `extern`, `Linker` seeks to resolve this and searches through `object files`, if not resolved, searches in `libraries.a`, if not then it searches in `linker script (ld)` itself.

```c
.bss{   
    startBSS = .
    bss_s = .
    bss_start = .  
    (*.bss)      
    (*.bss.*)  
    endBSS = .
} > RAM 
```

3 labels are created for the start address of `.bss` which means `startBSS`, `bss_s` and `bss_start` all contain the same location. `endBSS` label contains last location of `.bss` (actually contains first location after `.bss` so it is not written to zero in startup code).

![alt text](image-14.png)

- `.bss(no load): ALIGN(4)`: no load -> section exists in one memory, alignment set to 4 bytes.
- `.data{}>RAM AT>FLASH`: since `.data` section locations are reserved in RAM but values are stored in Flash. At least 3 locations are needed to perfom data copying (src_start/src_end & dest_start).

```c
_sdata = .                  //RAM address
_sidata = LOADADDR(.data)   //Flash address
```

- `ENTRY(_start)`: entry point for code is a function called `_start`.
- `FILL(0xFF)`: padding.
- Creating multiple labels for same location for generalization over different startup codes.
- Can't create label mid-allocation (ex: in the middle between `*.bss` sections).

## Specifying variable location

Placing a variable in a desired physical address can be achieved through the following steps:

- In code, add attribute for variable to be placed in a new section (`mySect`).
- In `Linker script`, create `memory` (`myMem`) with `origin` as desired address for variable and `size` set to variable size.
- In `Linker script`, create `section` (`mySect`) that the variable is set to be placed in and set its target memory (`myMem`).

## Notes

Creating a memory inside another memory is valid until assigning values which will result in `Linker Error: Overlapped`.

In case of variables set in different sections (ex: not in `.bss`) will cause them to not be initialized (startup code).

Creating variables in specific physical addresses can be used for `post build configuration` in which flashing of this location only is valid with no need for recompilation.

`estack = RAM origin + length`: init value for `SP` (last location in RAM)
