## Brake By Wire ECU
# Overview

Braking will be handled by a linear actuator that will actuate the brake lever which will pressurize the hydraulic system
closing the caliper and slowing the car. This ECU will act as an interface with this board, abstracting it from the rest of the
system. The actuator will sit on a bus with only this ECU on it, and this ECU will also junction with the priority bus.

This board will also take a pedal input, acting as a brake by wire system with auton kill, just like the throttle.

# Inputs

- **Priority CAN** - `Set Brake` messages
- **0-5k Pedal** - For manual braking

# Outputs
- **Actuator CAN** - Vendor specific messages
- **Priority CAN** - `Auton Disable` messages

# Algorithm

The esc should always write Set Brake messages to the break, as some nodes like AEB may use braking in the future. However,
we will still send a Auton Disable message when the pedal is pressed, to disable the rest of auton and prevent the PC from
controlling the brakes.

# Functional Requirements
- REQ1: The ECU will send a `Set Brake` heartbeat message to the actuator every 100ms with the last received braking distance, unless otherwise specified in another requirement.
- REQ2: If a Lock Brake message is received, then future break messages from CAN are ignored, and this ECU will no longer send heartbeat messages to the brake.
- REQ3: If an Unlock Brake message is received, then the current break distance is set to 0, future break messages are actioned on, and this ECU will begin to send heartbeat messages to the brakes again.
- REQ4: On `Set Brake` message, translate the included braking percent to the actuators actual CAN message type and send it on the actuator bus.
Also update the internal state for the heartbeats distance.
- REQ5: On pedal input, send a message to the brake proportional to that input, then send `Auton Disable` to the interface board. Until 
another `Set Brake` message is received, do not send more `Auton Disable` messages.
