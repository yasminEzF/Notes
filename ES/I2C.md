# Inter-Integrated Circuit Protocol (I2C)

## Bus Topology

![image-8](https://github.com/yasminEzF/Notes/assets/109252157/df05e5b5-83be-4b5d-8c4a-d92f7a1e734e)

Connection is `open-drain`, where writing `LOW` to bus results in `LOW` over bus while writing `HIGH` results in leaving the bus floating. If connection was `Push-Pull`, writing `LOW` would write `LOW` on bus and writing `HIGH` would write `HIGH` which would eventually lead to shortcircuiting on the bus.

(for the actual physical logic and transistors I don't get it nor do I care to)

Control | Open-drain | Push-Pull
---------|----------|---------
 0 | 0 | 0
 1 | floating (Bus VCC) | 1

Dominant Bit is `0`, recessive bit is `1`.

Ech node on the bus has a pre-determined address.

## Frame

![image-12](https://github.com/yasminEzF/Notes/assets/109252157/9205a14d-c402-450c-a1fe-0d8f6a2d0618)

Field | Sent by | Description
---------|----------|---------
 Start Condition | Master | transition of `SDA` line from `high (idle)` to `low` during `SCK` being `high`
 Slave address | Master | 7-bits
 Slave Address | Master | 7-bit
 R/W | Master | 0 for `write`, 1 for `read`
 Ack | Slave | 1-bit (`low` for ACK)
 Data | Data Sender | 8-bits
 Ack | Data Receiver | 1-bit (`low` for ACK)
 Stop Condition | Master | transition of `SDA` line from `low` to `high (idle)` during `SCK` being `high`

`Master` sends `frame header` (start condition + slave address + r/w command) and expects `Ack` from the node with the matching address, once recieved as a positive Ack, the frame continues with the data sent by the specified sender according to the control bit, then an Ack follows by the reciever accordingly.

This frame arrangement causes low throughput `:(` which is the ratio between the actual data bits in the frame to the frame's total number of bits.

$Throughput = 8 / (7+1+1+8+1) = 44 \%$

## Arbitration

Since I2C is a multi-master multi-slave protocol, what happens if two masters want to transmit at the same time and send start condition? `Arbitration` occurs to determine bus master whose message will be sent on the bus while any other losing master will wait until frame is finished for another chance to win arbitration.

Since bus is configured as open-drain connection, writing `low` to the bus is dominant over writing `high` which would leave the bus as floating, making arbitration determined depending on `Slave Adress` with the MSB's being zeroes, and in case of same slave address, determined for `write` command which is a bus `low`, if both want to perform the same command, then both will recieve slave `Ack` and `data` bits will be the ones to determine bus `Master`.

![image-10](https://github.com/yasminEzF/Notes/assets/109252157/3b74aa76-d3fe-489f-a4b4-9be2134e84cf)

Master needs to monitor bus constantly to check if lost arbitration or if it is still Bus Master.

Address `0b000 0000` is reserved as broadcast address to be recieved by all slaves.

## Interfacing with EEPROM

![image-11](https://github.com/yasminEzF/Notes/assets/109252157/5298f34f-85e9-4275-80fb-260dcbbdeae2)

EEPROM has set commands to communicate so the first frame would always be set to `write` the desired command to EEPROM, and then the second frame would follow to send the data depending on what was specified in the frame before.

Operation | Frame1 | Frame2
---------|----------|---------
 read register | `write` + EEPROM_read_command | `read` + Data_from_EEPROM
 write register | `write` + EEPROM_write_command | `write` + Data_to_EEPROM

## Other Features

### 1. Repeated Start

`Bus Master` won arbitration and wants to send multiple frames without losing it, so stop condition is skipped to start sending the next frame directly. Can be used for a master that is sending frames for:

- multiple slaves.
- sent command to slave and wants to read response.

### 2. No End Frame

Sending multiple data to the same address can be done by starting the frame as normal and alternating between sending and recieving data and Ack until all data is sent, then stop condition.

### 3. Clock Stretching

Clock stretching is done when Master is sending data faster (1bit/clk) than the slave can recieve. Slave can hold the clk to `low` until ready to recieve new data and master can't send until clk is back on bus as normal.

## I2C cons

- Bus starvation through (clock stretching, repeated start or no end frame)
- low throughput
- low speed
  - frame overhead
  - bit monitoring
  - physical layer
- not used over long distance
- address might be preconfigured in HW

Speeds:

- normal (100 kb/sec)
- high (400 kb/sec)
- ultra (3.4 Mb/sec)

## Notes

Two Wire Interface is the same as I2C but for trademark purposes introduced by Atmel.

I2C addressing is 7-bits but with the exception of 16 reserved addresses (`0b1111xxx` and `0x0000xxx`).

one of the reserved addressses defines an extended address mode where it works with 10-bit addresses.
