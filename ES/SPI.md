# Serial Peripheral Interface (SPI)

Single-Master Multi-Slave.

Sync.

## Connection

4-Wire Connection (Full-Duplex)

![alt text](image-13.png)

Selecting slave to recieve is done by pulling `Chip Select (CS)` pin to `low`, but max number of slaves is limited to number of Master DIO pins.

![alt text](image-14.png)

SPI contains a shift register that is shifted by 1-bit for each clock, moving data out of the register while recieving data into it at the same time.

3-Wire Connection (Simplex)

![alt text](image-15.png)

4-Wire to 3-Wire

![alt text](image-16.png)

(Resistance due to different currents to prevent short circuiting? ~~not sure~~)

Daisy Chain (not used)

![alt text](image-17.png)

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

![alt text](image-18.png)

Clock phase defines whether `read/sample` operation or `write/setup` operation is performed first.

![alt text](image-19.png)

Both nodes should have same configuration to be one of the following options:

 | Phase = 0 | Phase = 1
---------|----------|---------
 Polarity = 0 (idle = `low`)| `read` at `leading (rising)` | `write` at `leading (rising)`
 |`write` at `trailing (falling)`|`read` at `trailing (falling)`
 Polarity = 1 (idle = `high`)| `read` at `leading (falling)` | `write` at `leading (falling)`
 |`write` at `trailing (rising)`|`read` at `trailing (rising)`
