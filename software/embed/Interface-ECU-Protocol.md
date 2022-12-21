# Interface protocol

This is a description of the communication protocol that will sit on top of RS232 for the
UART between the PC and interface MCU.

The protocol is designed to basically just be minimized CAN2.0 frames, as that is the main 
data we will be transmitting.

## Packets
Each packet will have the following layout:
1. 1 byte type id
2. 2 byte data length (can be zero)
3. 0..512 byte data, as indicated above

The type id should be the same as that types arbitration id on the CAN bus.
It should generally also share the same data as was on the CAN bus, for simplicity's sake.

As an example, here is an Enable Motor message:
```
type id: TODO
dlc: 0x0
data: null
```
as you can see here, it's possible for a packet to have no data.