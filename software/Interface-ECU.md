# Interface ECU

_Also see [the interface protocol](./Interface-ECU-Protocol.md)_

## Overview 
The interface ECU will act as the bridge between ROS on the PC and CAN
in the ECU network. It communicates over UART.

## Inputs
- **UART from PC** - Over the virtual COM port on the usb
- **Perception CAN** - Messages from the perception bus
- **Auton switch** - Physical switch to toggle auton state, physical equivalent to `Auton Disable` CAN message

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
- Auton Toggle

## Algorithm

## Functional Requirements

- REQ1: Steering, throttle, and break messages from the PC must be passed to the CAN bus.
- REQ2: Encoder messages on the CAN bus must be sent to the PC.
- REQ3: The ECU must process messages in full duplex.
- REQ4: This ECU should have a failsafe such that, if this ECU stops processing, the Estop will be fired.
- REQ5: On `Auton Disable` CAN message, stop acting on messages from the PC. Send a message to the PC, where ROS will then state transition to teleop. 
This should be stored as internal state.
- REQ6: On the Auton switch being toggled, stop acting on messages from the PC. Send a message to the PC, where ROS will then state transition to teleop. This should be stored as internal state, shared with REQ5.