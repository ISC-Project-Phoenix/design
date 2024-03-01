# Interface ECU

_Also see [the interface protocol](./Interface-ECU-Protocol.md)_

## Overview 
The interface ECU will act as the bridge between ROS on the PC and CAN
in the ECU network. It communicates over UART.

## Inputs
- **UART from PC** - Over the virtual COM port on the usb
- **CAN** - Messages from CAN
- **Estop Tap** - Pin that is set high when estop is not enabled, and pulled low when the kart is killed.

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

## Estop

When the estop is active (falling edge), the board will send a killed message to ROS. This will then transition ROS
into the killed state, putting the kart in teleop. This just allows us to better model the state of the hardware in
ROS, allowing us to do things such as stop PID loops. 

When the estop is disabled (rising edge), the board will send an enabled message to ROS. This will transition ROS
to active, allowing for teleop or a transition back to auton.