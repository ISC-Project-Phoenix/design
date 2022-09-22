# CAN Mappings

This file contains proposed CAN ID mappings for various messages defined for use in phoenix.

TODO: First define all messages, then go through and assign IDs. After that, define data encoding.

## Motor control

- **Set Speed** - Sets the motor speed to the contained speed percent.
  - encoding - fixed point percent, encoded as LE u8.

## Steering control
- **Set Angle** - Sets the steering motor to a certain angle, and holds it.
  - encoding: [see doc](images/Steering%20motor%201.png). We will use command mode position to use the motor as a big servo.
Angles are sent in variable 1, in "Position in revolutions", whatever that unit is.

## Brake Control
- **Set Brake** - Sets the linear actuator for the break to a certain distance. Note that this command both
controls the clutch and motor of the actuator, so it must be used properly to first engage the clutch,
then the motor, then disable both.
  - encoding: [see doc](images/Linear Actuator CAN Reference 2.png)
- **Lock Brake** - Prevents further braking messages from being sent from the interface to the bus.
- **Unlock Brake** - Lets more braking messages be sent to the bus, if locked.

## Sensor Data
- **Encoder Count** - Current encoder count, as well as current velocity. TODO semantics
- **Range Data** - Generic range data from the front sensor. Should be generic across sonics, radar, ToF ect. 
  - encoding: IEEE float 32, in meters.