# CAN Mappings

This file contains proposed CAN ID mappings for various messages defined for use in phoenix.

TODO: First define all messages, then go through and assign IDs. After that, define data encoding.

## Motor control

- **Set Speed** - Sets the motor speed to the contained speed percent.
  - encoding - fixed point percent, encoded as LE u8.

## Steering control
- **Set Angle** - Sets the physical servo to the passed angle. (see servo docs)

## Brake Control
- **Set Brake** - Sets the Brake pressure. (see actuator docs)
- **Lock Brake** - Prevents further braking messages from being sent from the interface to the bus.
- **Unlock Brake** - Lets more braking messages be sent to the bus, if locked.

## Sensor Data
- **Encoder Count** - Current encoder count, as well as current velocity. TODO semantics
- **Range Data** - Generic range data from the front sensor. Should be generic across sonics, radar, ToF ect. 
  - encoding: IEEE float 32, in meters.