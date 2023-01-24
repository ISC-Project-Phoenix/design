# Phnx_io_ros AKA Can interface

## Summary

Bridges ROS to the CAN network. Our single point of actuation.

See [the interface docs for more info](../embed/Interface-ECU.md).

Also see [gz_io_ros](gz_io_ros.md) for the fake of this node used in sim.

### Config

The following config values refer to fields of /ack_vel messages:
- `max_throttle_speed` - Velocity at which we consider the throttle fully pressed.

- `max_braking_speed` - Negative velocity at which we consider the brake fully pressed.

### Publishes

- `/odom_ack` - AckermannDrive messages representing the current state of the kart. See module readme for more info.
    - This should be built by saving the last message from /ack_vel, then copying the velocity field (the throttle
      input) to the acceleration field, then writing the next encoder velocity into the velocity field and publishing.

### Subscribes

- `/ack_vel` - Output from `twist_to_ackermann`. Braking commands are encoded as negative velocity.

### Services Used

- `/robot/set_state` - service on `robot_state_controller`. Sets state to KILL or PAUSE when the estop/pause is
  triggered, or
  if the pedals are pushed. This should stop `drive_mode_switch` from accepting any twist, regardless of drive mode.
  ACTIVE is sent
  when estop or pause are undone.
