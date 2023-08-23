## Steering ECU

# Overview

This ecu will sit in front of the steering motor in order to abstract it away from the rest of the bus. This is the same
idea as the brake ECU.

This ECU should be designed with ease of change in mind, since it's goal is to make changing the motor out easier.

Note that this ecu assumes that the steering motor releases when the user has input, else this ecu will need to be 
amended to make sure the driver's arms remain attached.

# Inputs

- **Steering motor** - Not necessarily a CAN line, could be PWM or other protocol
- **CAN** - `Set Angle` messages

# Output 
- **CAN** - `Get Angle` messages

# Functional Requirements
- REQ1: On receiving a `Set Angle` message, the steering motor will hold that selected angle until another `Set Angle` message is received.
- REQ2: Whenever feasible for the given motor, a `Get Angle` message should be sent. 