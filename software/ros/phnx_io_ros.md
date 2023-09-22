# Phnx_io_ros AKA Can interface

## Summary

Bridges ROS to the CAN network. Our single point of actuation.

This node also contains our speed control loop (essentially cruse control). Basically, this node will take command
messages in `/ack_vel` which contain both desired steering angle (which is handled on the bus) and a desired velocity.
This node will then pass this set speed to a control loop, which will run in a background thread. This loop will be
ticked
by odom to close the loop, producing both throttle and brake commands on the bus as needed to meet the set speed.

See [the interface docs for more info](../embed/Interface-ECU.md).

Also see [gz_io_ros](gz_io_ros.md) for the fake of this node used in sim.

### Publishes

- `/odom_ack` - AckermannDrive messages representing the current state of the kart. See module readme for more info.
    - This should be built by saving the last message from /ack_vel, then copying the velocity field (the throttle
      input) to the acceleration field, then writing the next encoder velocity into the velocity field and publishing.
- `/odom_can` - Normal Odom message, but with only velocity filled with the encoder value off the bus.

### Subscribes

- `/ack_vel` - AckermanDrive command to actuate on the kart.
- `/odom` - Velocity is taken from odom to close the speed control loop. As such, the frequency of this topic will
  control
  the frequency of the control loop.

### Services Used

- `/robot/set_state` - service on `robot_state_controller`. Sets state to KILL or PAUSE when the estop/pause is
  triggered. This should stop `drive_mode_switch` from accepting any twist, regardless of drive mode.
  ACTIVE is sent
  when estop or pause are undone.

### Control Loop Algorithm

TODO

Current idea is to use two separate control loops, one for throttle and one for brake. When odom speed is > the set 
speed, then the throttle is released and the brake control loop is used until the desired speed is met. Reverse is true
for throttle. There is probably a lot of nuance I do not understand with this.

Ultimately, this loop should be implemented as a class in a separate header file, so it can be easily tested outside
of ROS.
