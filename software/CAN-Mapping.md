# CAN Mappings

This file contains proposed CAN ID mappings for various messages defined for use in phoenix.

TODO: First define all messages, then go through and assign IDs. After that, define data encoding.

## Motor control

- **Set Speed** - Sets the motor speed to the contained speed percent.
  - encoding - fixed point percent, encoded as LE u8.
- **Disable Motor** - Turns the motor off, and prevents all commands until re-enabled.
- **Enable Motor** - Allows for the motor to react to `set speed` commands, if disabled.

## Steering control
- **Set Angle** - Sets the virtual ackermann wheel to the contained angle.
  - encoding - Angle is in radians, negative is right, positive is left. Encode as IEEE single.

## Brake Control
- **Set Brake** - Sets the Brake pressure to the contained percent.
  - encoding - fixed point percent, encoded as LE u8.

## Sensor Data
- **Encoder count** - A updated encoder count. TODO semantics
- **Radar data** - To spec of radar.