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

TODO make this a list of how we solved requirements

Simply convert the PC messages to the appropriate CAN messages.
If a `Lock Brake` message is received, no more brake messages should be transmitted.
Once a `Unlock Brake` message is received, break messages can flow again. This ECU and the 
[pause ECU](Wifi-Pause.md) share a duty of keeping the [brake by wire](1A0014B-Linear-Actuator.md) alive.

## Functional Requirements

- REQ1: Steering, throttle, and break messages from the PC must be passed to the CAN bus.
- REQ2: Encoder messages on the CAN bus must be sent to the PC.
- REQ3: The ECU must process messages in full duplex.
- REQ4: The ECU will send a `Set Brake` heartbeat message to the priority CAN bus every 100ms with the last received braking distance,
unless otherwise specified in another requirement.
- REQ5: If a `Lock Brake` message is received, then future break messages from the PC are ignored, and this ECU will no longer
send heartbeat messages to the brake.
- REQ6: If an `Unlock Brake` message is received, then the current break distance is set to 0, future break messages are allowed to be sent, 
and this ECU will begin to send heartbeat messages to the brakes again.
- REQ7: This ECU should have a failsafe such that, if this ECU stops processing, the Estop will be fired.