# Serial Peripheral Interface (SPI)

Single-Master Multi-Slave.

Sync.

## Connection

4-Wire Connection (Full-Duplex)

![image-13](https://github.com/yasminEzF/Notes/assets/109252157/e57ce3ea-d21e-4dc4-925f-08e72c65b74f)

Selecting slave to recieve is done by pulling `Chip Select (CS)` pin to `low`, but max number of slaves is limited to number of Master DIO pins.

![image-14](https://github.com/yasminEzF/Notes/assets/109252157/7e9a6a0b-523f-480d-bcd0-c6af82e55020)

SPI contains a shift register that is shifted by 1-bit for each clock, moving data out of the register while recieving data into it at the same time.

3-Wire Connection (Simplex)

![image-15](https://github.com/yasminEzF/Notes/assets/109252157/1df38e60-1483-4c68-960f-7323ae3390bb)

4-Wire to 3-Wire

![image-16](https://github.com/yasminEzF/Notes/assets/109252157/b69be6ba-14d3-4c49-81cf-ed44f0f870fa)

(Resistance due to different currents to prevent short circuiting? ~~not sure~~)

Daisy Chain (not used)

![image-17](https://github.com/yasminEzF/Notes/assets/109252157/037170f6-63f1-41f1-9100-3a36c9857184)

In summary, each slave works as master to next slave in chain, sending data to n-ordered slave needs master to send n-bytes.

unlimited number of slaves but a single slave failure will fail whole network.

## Frame

Whole frame is data as sending/recieving and node addressing is handled through HW. Not an advantage as it means there is no error checking mechanisms.

$Throughput = 100\%$

Master responsibilities are:

- initiate clock
- choose slave
- start sending data

## Clock Polarity & Phase

Clock polarity defines idle state for clock, and so first edge would be a `leading` edge, while the other is a `trailing` edge.

![image-18](https://github.com/yasminEzF/Notes/assets/109252157/affd42ba-533b-493d-8016-ee14a31ed52d)

Clock phase defines whether `read/sample` operation or `write/setup` operation is performed first.

![image-19](https://github.com/yasminEzF/Notes/assets/109252157/cdd85511-fa0e-4d30-a8f9-4b6ee5d04b83)

Both nodes should have same configuration to be one of the following options:

| | Phase = 0 | Phase = 1
|---------|----------|---------
| Polarity = 0 (idle = `low`)| `read` at `leading (rising)` | `write` at `leading (rising)`
| |`write` at `trailing (falling)`|`read` at `trailing (falling)`
| Polarity = 1 (idle = `high`)| `read` at `leading (falling)` | `write` at `leading (falling)`
| |`write` at `trailing (rising)`|`read` at `trailing (rising)`
