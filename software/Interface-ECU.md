# Interface ECU

_Also see [the interface protocol](./Interface-ECU-Protocol.md)_

## Overview 
The interface ECU will act as the bridge between ROS on the PC and CAN
in the ECU network. It communicates over UART.

## Inputs
- **UART from PC** - Over the virtual COM port on the usb
- **Perception CAN** - Messages from the perception bus

## Outputs
- **UART to PC** - Sends select CAN messages to the PC. Currently these are all perception
- **Priority CAN** - Sends messages from the PC to CAN. Currently these are all high priority

## Messages 

These are all in the same format as described in the can messages.

### From PC
- Steering
- Throttle
- Brake

### From CAN
- Encoder

## Algorithm

Simply convert the PC messages to the appropriate CAN messages.
If a `Lock Brake` message is received, no more brake messages should be transmitted.
Once a `Unlock Brake` message is received, break messages can flow again. This ECU and the 
[pause ECU](Wifi-Pause.md) share a duty of keeping the [brake by wire](1A0014B-Linear-Actuator.md) alive.