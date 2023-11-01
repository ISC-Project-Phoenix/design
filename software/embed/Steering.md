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

# Notes
Because the steering motor is likely to have a gearbox attached, we must consider how the set angle will apply after the reduction. We need to test to ensure this is linear.

For example, setting to 100 degrees with a 10:1 reduction should only turn the output shaft to 10 degrees. Thus, we must actually turn the desired angle * reduction ratio degrees.

The Teknek motor has the ability to change what its max steering angle is at max PWM via firmware, so we should just have to set this to whatever our actual max steering angle is * reduction. Then in this ECU, we read the angle off the bus, calculate the percent turn by dividing it with the actual max steering angle, and finally apply that same percentage out as the PWM duty cycle. Because the motor max already takes the reduction max into account, this will turn the motor the actual distince required to hold our requested angle.
