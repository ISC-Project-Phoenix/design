# Phnx_io_ros AKA Can interface

## Summary

Bridges ROS to the CAN network. Our single point of actuation.

See [the interface docs for more info](../embed/Interface-ECU.md).

Also see [gz_io_ros](gz_io_ros.md) for the fake of this node used in sim.

### Publishes

- `/odom` - odom generated from embedded sources, currently just the encoder, so only velocity needs to be recorded.

### Subscribes

- `/ack_vel` - Output from `twist_to_ackermann`. Braking commands are encoded as negative velocity.

### Services Used

- `/robot/set_state` - service on `robot_state_controller`. Sets state to KILL or PAUSE when the estop/pause is
  triggered, or
  if the pedals are pushed. This should stop `drive_mode_switch` from accepting any twist, regardless of drive mode.
  ACTIVE is sent
  when estop or pause are undone.
