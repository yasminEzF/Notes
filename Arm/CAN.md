# CAN

creating a protocol is limited by HW constrains, for example if a proposed protocol is to be 3-bits or 11-bits, HW components would be required that have the suitabl response time to detect and measure these signals (bits). A HW peripheral would be needed to extract the data accordingly and perform necessary security checks

## Physical Layer

![image](https://github.com/yasminEzF/Notes/assets/109252157/7c689e85-82a9-480f-9b38-c4c87617845f)

CAN communication needs a CAN controller and tranciever, that depends on the voltage difference between 2 wires

the CAN bus is a twisted pair, so communication can be achieved over long distance as any noise would affect both wires similarly and maintain the same voltage difference

CAN communication is event-driven, with no master or slave

There are 2 types of trancievers for different speeds:

- CAN low speed (125 kbps)
- CAN high speed (1 Mbps)

|Logical | Low Speed | High Speed
|---------|----------|---------
| | `3.6 V` | `1 V`
| 0 | H = 3.6 V | H = 3.5 V
|  | L = 1.4 V | L = 1.5 V
| 1 | H = 0 V | H = 2.5 V
|  | L = 5 V | L = 2.5 V

in CAN low speed, voltage switching range is 3.6 volts while CAN high speed switches 1 volt only so it is faster

![image](https://github.com/yasminEzF/Notes/assets/109252157/ec5b9c84-7995-489e-863e-63dd08ee1077)

## arbitration

since there are no masters on Bus, how is it determined which message is being transmitted and how is it not corrupted by the rest?

each node performs `bit-monitoring` to check if its message is the one on the bus or if it lost arbitration and another node has ownership

![image](https://github.com/yasminEzF/Notes/assets/109252157/2951af84-3a98-4cb9-ae0d-71e7c5dfbd36)

if duraning transmission of `Event A` message, Evenets B and C are triggered, after event A end of frame, arbitration will occur to determine which of the two messages gets transmitted first

![image](https://github.com/yasminEzF/Notes/assets/109252157/5664b3c6-c87d-4066-81a1-66b81a3f3bf3)

in a scenarion where `N0`, `N1`, `N2` want to transmit on the bus at the same time, each node and the Bus will have the following values

||`N0`|`N1`|`N2`|`CAN Bus`
|---------|---------|----------|---------|---------
|`T0`|0|0|1|0
|**TX**|0|0|1|
|**RX**|0|0|0|
|`T1`|1|1|-|1
|**TX**|1|1|||
|**RX**|1|1|||
|`T2`|1|0|-|0
|**TX**|1|0|||
|**RX**|0|0|||
|`T3`|-|win|-|

`N1` and `N2` lost arbitration, so they wil wait for next frame to try again, also if any other node wants to transmit a message while a frame is being sent on the bus, it will have to wait as well

multiple nodes transmit on the Bus at the same time at frame start to try and win arbitration, or at end of frame?

## frame types

in CAN communication, there is no guarantee that any message will be sent

### 1. Data frame

   ID + data frame + data

### 2. Remote frame

   remote frame is not commonly used, its main functionality is to request a message by its ID so that its owner would send it. if bus architecture is done correctly, there will be no use for the remote frame (in automotive)
   it increases bus load uselessly
   ID + remote frame

### 3. Error frame

   ![image](https://github.com/yasminEzF/Notes/assets/109252157/33ae69e7-026a-4315-916d-82c2a83db2cc)

### Bus Matrix

bus matrix doesn't have timestamps, it only contains data about each message (sender/owner and recievers/listeners). in LIN, bus matrix had time slot and all frames were set to be sent at specific times by master

|Msg ID | N0 | N1 | N2
|---------|----------|---------|---------
| 0x01 | S | R | -
| 0x02 | R | S | R
| 0x05 | R | - | S

## frame

![image](https://github.com/yasminEzF/Notes/assets/109252157/c819d4a8-7d6a-409c-8eb3-b64aff0d4d4a)

- `SOF`: 1-bit LOW
- `arbitration field`: 11/32-bits
  - `Msg ID`: 11-bit
  - `Remote Transition Request (RTR)`: 1-bit (LOW for data, HIGH for REMOTE)
- `control field`:
  - `Identifier Extension (IDE)`: 1-bit (LOW for normal frame, HIGH for extended frame)
  - `reserved (r)`: 1-bit LOW
  - `Data Length Code (DLC)`: 4-bits (0 ~ 8 Bytes data)
- `Data field`: 0 ~ 8 bytes of data
- `CRC field`:
  - `CRC sequence`: 15-bits, calculated by HW for each bit in the frame up until this field
  - `CRC delimiter`: 1-bit set to HIGH
- `Ack field`:
  - `Ack`: 1-bit sent by reciever (LOW for positive Ack, HIGH for negative Ack)
  - `Ack delimiter`: 1-bit set to HIGH
- `EOF`: 7-bits set to HIGH
- `Intermission (ITM)`: 3-bits set to HIGH

![image](https://github.com/yasminEzF/Notes/assets/109252157/02091f2a-f52c-4ee6-99bc-6ee80dff29c3)

in case of extended frame, the `MsgID` is composed of 29-bits, 11 of which are sent as in standard data frame, while the rest 18-bits are sent after `IDE` field (set to HIGH) and `Substitute Remote Request (SRR)` field (1-bit set to HIGH). after the extended ID, `RTR` is sent followed by 2 reserved bits set to LOW and the `DLC` follows as normal.

it is important to note in `Data Field` whether the most significant bytes are sent first or the least based on design, but on bit-level, most signifact bits are sent first.

sending MSB first allows for message ID's to be sorted as 0 (most important) to N-MsgID (least important) for correct arbitration.

## Error Checking

For any error checking mechanism, two things must be defined which are `field` that will be monitored for error and `checker` which is the component that will detect the error. `Error frame` sender is the `error` checker.

Checking for node failure is in the bus architecture phase that designs a monitoring app that checks messages for predefined minimum/maximum periodicity.

1. CRC by reciever
2. Ack by sender
3. bit monitor by sender
4. form check by reciever
5. bit stuffing by reciever

### 1. CRC

CRC is calculated by reciever and compared by the one calculated and sent by sender for each bit in the frame until the CRC field

### 2. Ack

Ack is checked by sender if a reciever replied with Ack. If no recievers exist on the bus, the sender will recieve a negative Ack (idle bus = HIGH)

### 3. Bit Monitor

|Node | Bus |
|---------|----------|---------
| 0 | 0 | OK
| 1 | 0 | Arbitration lost
| 0 | 1 | Error -> stop
| 1 | 1 | OK

bus is monitored throughout the whole frame to make sure that the message being transmitted is on the bus correctly except for `arbitration field` where mismatching would mean losing arbitration and `EOF` where it would mean that a reciever is sending an error frame.

### 4. Form Check

form check is for `Ack`, `CRC` delimiters and `EOF`. if any of them is set to LOW, that would indicate a format error and lead to error handling.

### 5. Bit Stuffing

![image](https://github.com/yasminEzF/Notes/assets/109252157/68982b1b-9de2-4a6c-ae0a-5075517d6fa0)

Sender injects a different bit every 5 consecutive similar bits and on the other side, reciever compares arriving bit stream for 5 consecutive similar bits and removes the fifth to filter the data correctly. if 6 consecutive similar bits are found, then it would indicate an error in the stream.

## `NOTES`

- NRZ encoding, Manchester encoding as a concept bas farhadt CAN tbh
- smth about error frames (check vector elearning)
- read more abt CAN FD (flexible datarate 64-byte) & extended data frame
- ![image-3](https://github.com/yasminEzF/Notes/assets/109252157/594ea82c-2afc-4b0a-b9dd-77f6154a17c8)

