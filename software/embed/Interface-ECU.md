# Interface ECU

_Also see [the interface protocol](./Interface-ECU-Protocol.md)_

## Overview 
The interface ECU will act as the bridge between ROS on the PC and CAN
in the ECU network. It communicates over UART.

## Inputs
- **UART from PC** - Over the virtual COM port on the usb
- **CAN** - Messages from CAN

## Outputs
- **UART to PC** - Sends select CAN messages to the PC. Currently these are all perception
- **CAN** - Sends messages from the PC to CAN

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
- REQ5: On `Auton Disable` CAN message, stop acting on messages from the PC. Send a message to the PC, where ROS will then state transition to inactive. 
This should be stored as internal state.
- REQ6: On the Auton switch being toggled, stop acting on messages from the PC. Send a message to the PC, where ROS will then state transition to inactive. This should be stored as internal state, shared with REQ5.
- REQ7: On `Training mode` message, relay `Set Throttle`, `Set Brake`, `Set Steer`, `Encoder Count` to the PC when received.
- REQ8: This ECU should be able to read the current status of the hardware pause and estop by reading connected pins.
- REQ9: If pause pin or estop pin go high, send message to the PC, where ROS will then transition system state to inactive.
