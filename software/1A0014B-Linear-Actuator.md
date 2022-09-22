# 1A0014B 3" Linear CAN Actuator CAN Specification

## Overview
The 1A0014B 3" Linear CAN Actuator uses J1939 CAN 2.0b frames at a baud rate of 250k in order to extend to a certain position in
inches where zero is fully retracted and 3" is fully extended. Below is the CAN message specification and
message examples as well as important usage information to prevent undo wear and tear on the actuator.

## Important Usage Information:
The linear actuator will power off both the clutch and motor and go into safe mode if 
no message is received by the actuator for 1 second. It is recommended to resend commands
every 100ms.

In order to prevent accelerated wear on the actuators clutch it is recommended to engage the
clutch at least 20ms before engaging the motor and disengaging the motor at least 20ms before
disengaging the clutch.

## CAN Position Command Message Structure
Below is the basic command message structure with examples as well as documentation on how to
assign user defined CAN ID's. For more information see the [full specification here.](./pdfs/LINEAR%20ACTUATOR%20-%201A0011BJ.pdf)
![alt text](images/Linear%20Actuator%20CAN%20Reference%201.png)
![alt text](images/Linear%20Actuator%20CAN%20Reference%202.png)
![alt text](images/Linear%20Actuator%20CAN%20Reference%203.png)

# Command Structure for User Defined CAN ID's
![alt text](images/Linear%20Actuator%20CAN%20Reference%204.png)