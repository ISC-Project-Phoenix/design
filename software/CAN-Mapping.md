# CAN Mappings

This file contains proposed CAN ID mappings for various messages defined for use in phoenix.

## Priority and IDs
The following are all CAN2.0B extended identifiers. There exists no remote or error frames on the bus.

- (0x0000000) **Auton Disable**
- (0x0000001) **Set Brake**
- (0x0000002) **Lock Brake**
- (0x0000003) **Unlock Brake**
- (0x0000004) **Set Angle**
- (0x0000005) **Set Throttle**
- (0x0000006) **Encoder Count**
- (0x0000007) **Range Data**

## Master control

- **Auton Disable (0x0000000)** - Tells the interface board to stop sending messages from ROS to the CAN network. 
The interface board should send a message to the PC, where ROS will state transition to teleop. There will be no auton enable 
message, rather you will need to toggle auton via a physical switch. 

## Motor control

- **Set Speed** - Sets the motor speed to the contained speed percent.
  - encoding - fixed point percent, encoded as LE u8 in the first data byte.

## Steering control
- **Set Angle** - Sets the steering motor to a certain angle, and holds it.
  - encoding: [see doc](images/Steering%20motor%201.png). We will use command mode position to use the motor as a big servo.
Angles are sent in variable 1, in "Position in revolutions", whatever that unit is.

## Brake Control
- **Set Brake** - Sets the brake to a certain percent engagement.
  - encoding: fixed point percent, encoded as LE u8 in the first data byte.
- **Lock Brake** - Prevents further braking messages from being sent from the interface to the bus.
  - encoding: no data
- **Unlock Brake** - Lets more braking messages be sent to the bus, if locked.
  - encoding: no data

## Sensor Data
- **Encoder Count** - Encoder ticks since last CAN message, as well as current velocity.
  - encoding: First 2 bytes are the encoder ticks as a u16, bytes 3-7 are the current velocity in m/s as an IEEE float 32.
- **Range Data** - Generic range data from the front sensor. Should be generic across sonics, radar, ToF ect. 
  - encoding: IEEE float 32, in meters.