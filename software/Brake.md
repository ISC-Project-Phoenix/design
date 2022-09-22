## Brake By Wire ECU

# Overview

Braking will be handled by a linear actuator that will actuate the brake lever which will pressurize the hydraulic system
 closing the caliper and slowing the car.

# Inputs

- **Priority CAN** - For setting the brake actuator to a certain position. For more information on the CAN message
specification please refer to the [1A0014B 3" Linear Actuator CAN Specification](./1A0014B-Linear-Actuator.md)

# Outputs

Movement of the linear actuator to depress the brake

# Algorithm

The linear actuator takes in messages as a position to move to in inches over CAN 


