# Bootloader

## In-curcuit programming vs In-App programming

When using `In-curcuit Programming (ICP)`, program is flashed using a debugger through `JTAG` or `SWI` but after product is packaged, access is lost to these interfaces. What if bugs appear and the need to reflash these bug fixes arises?

![image](https://github.com/yasminEzF/Notes/assets/109252157/6f1a463e-25de-4f45-84eb-ad848284715c)

In order to be able to reflash the program after product packaging, `Flash` is divided to contain two programs. A `bootloader` or a flashing application that is flashed by a debugger and the `product` application that can be reflashed using the `bootloader` through any communication interface.

![image-1](https://github.com/yasminEzF/Notes/assets/109252157/86cf2d06-c34a-4a3a-b447-8ceaaf46c933)

`Bootloader` should be able to perform the following functionalities:

- `Flash` program at 0x...
- `Erase` 0x...
- Erase and Flash
- Flash App1 at 0x0100, App2 at 0x0800
- `Detect` & `Boot` Application
- `Update` Application

`Bootloader` scenarion will run as follows, after `reset`, go to `vector table` or `bootloader`, run `bootloader startup`, jump to `bootloader main`, check if an `update request` exists, if exists then recieve `App update` and flash, if not, then check if `App` exists, if no `App` exists, do nothing, if `App` exists, jump to `App`.

`Bootloader` modules are:

- COM protocol (TP)
- Flash

## Flash (NVM)

Flash memory is `non-volatile` which means it preserves its contents even at power off. To write or Erase Flash, high voltage is required (composed of MOSFET's i guess idk read about it somewhere else) which can't be supplied by processor (that's why it is `READ-ONLY` for processor), so a `Flash Peripheral` is required which steps up the voltage so that the processor can Erase/Write on flash.

- Read
- Write/program (`High voltage`)
- Erase (`High voltage`)

Flash memory `write/Erase` is constrained by specific HW connection that allows for transmission of high voltage required. A memory size constrain appeared for `write` operation where memory can be written `page` by `page`, as for `erase` operation is constrained by a larger memory size on which it can perfom deletion which is a `sector`. `Page` and `Sector` sizes are HW specfications for Flash memory.

|Written | Erased
|--------|----------
| `0x00` | `0xFF`
| Logic Low | Logic High
| By `Page` | By `Sector`

Since `write` operation can only change `Logic High` to a `Logic Low` and not the opposite, `writing` a page to an already written `page` will result in errors. An `erase` operation is needed first to change all previously written bits to `High` then a new `write` operation can be performed.

In case a single byte needs modification, whole sector must be read in RAM then modified, after that `Sectore erase` is performed and then rewritten from RAM.

Performing `Mass Erase` would result in erasing of whole Flash which would mean deletion of the program that requested the `Erasure`.

![image-2](https://github.com/yasminEzF/Notes/assets/109252157/7b98b847-5f30-450b-bedb-3897dd5b5d26)

### Sector Erase

1. write `key1`
2. write `key2` (key sequence is security to unlock control register)
3. set `sector address`
4. set `sector erase` bit
5. wait until `operation done` flag (no timeout mechanism as there is no other functionality to perform that is blocked)

### Page Program

1. write `key1`
2. write `key2`
3. set `Page Program` bit
4. perform `write` instruction on page
5. wait until `operation done` flag

### Mass Erase

1. write `key`
2. write `key2`
3. set `mass erase` bit

In Embedded Apllications, the concepts of `self-update` and `mass erasure` are not used. Not all flash drivers support performing `fetch` from a sector while an operation is running on another sector.
