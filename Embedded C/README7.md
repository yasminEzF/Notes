# Embedded C Lec 7

## Build process

![image-4](https://github.com/yasminEzF/Notes/assets/109252157/4ecc9f08-c952-4268-b83a-d6dd5d961b2b)

## symbol table

`Symbol table` is a data structure maintained by `Compiler` that contains information about variables, functions, and other symbols to work as mapping between symbols in source code and their memory locations or values which makes it essential for linking and debugging.

- `symbol name`: name declared in source code
- `type info`: data type
- `memory location or value`
  - variable: memory address where it is stored
  - function: memory address for function code
  - constants: value
- `scope info`: local to a function or global
- `linking info`: internal or external (static to a file or externed)

| Name | Type | Logical Address | Direction
|---------|----------|---------|---------
| var1 | int | 0x100 | provided
| var2 | int | 0x200 | needed
| fun1 | void* (void) | 0x300 | needed

![image-5](https://github.com/yasminEzF/Notes/assets/109252157/9afc3d60-6382-458a-aa9a-c6fe0d92d1e7)

- `ABS`: has an absolute value that is not affected by relocation
- `UNDEF`: not provided in this file, should be linked externally (declared here, defined elsewhere)
- `COMMON`: uninitialized data object

![image-6](https://github.com/yasminEzF/Notes/assets/109252157/d4923770-c9b4-495f-9705-0c4d5a4fb46c)

`buf` is a data object, `g` because it is global, placed in `.data` section at offset `0x0000 0000` with size `8 byte` (2 * `sizeof(int)`).

`main` is a function, `g` because it is global, placed in `.text` section at offset `0x0000 0000` with size `0x14 byte`.

`swap` is an external variable `UND`.

![image-7](https://github.com/yasminEzF/Notes/assets/109252157/d5cc1f44-c704-4857-93b6-167711914cb3)

`bufp0` is a data object, `g` because it is global, placed in `.data` section at offset `0x0000 0000` with size `4 byte`.

`buf` is an external variable `UND`.

`bufp1` is a data object, `COM` because it is declared as `static`, which will be allocated in `.bss` during linking with `4-byte` alignment and `4-byte` size.

`swap` is a function, `g` because it is global, placed in `.text` section at offset `0x0000 0000` with size `0x35 byte`.

![image-8](https://github.com/yasminEzF/Notes/assets/109252157/f4e1a79c-9a8a-4bd0-a8af-1bc1b796de37)

## Libraries

![image-9](https://github.com/yasminEzF/Notes/assets/109252157/09e28532-68a3-4efb-9bf2-7b38aa04becf)

- `Static linking (.a/.lib)`: large binary with copy of libraries in the executable
- `Dynamic linking (.so/.dll)`: library name is linked in executable, actual linking at runtime.

### Static library creation

```bash
gcc -c file.c -o file.o
ar rcs libmylib.a file.o  #create
ar -t libmylib.a  #view contents
gcc main.c libmylib.a -o main.elf #linking
gcc main.c -L/lib/path -lmylib -o main.elf
```
