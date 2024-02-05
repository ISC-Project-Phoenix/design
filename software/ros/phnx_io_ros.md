# Phnx_io_ros AKA Can interface

## Summary

Bridges ROS to the CAN network. Our single point of actuation.

This node also contains our speed control loop (essentially cruse control). Basically, this node will take command
messages in `/robot/ack_vel` which contain both desired steering angle (which is handled on the bus) and a desired
velocity.
This node will then pass this set speed to a control loop, which will run in a background thread. This loop will be
ticked
by odom to close the loop, producing both throttle and brake commands on the bus as needed to meet the set speed.

See [the interface docs for more info](../embed/Interface-ECU.md).

Also see [gz_io_ros](gz_io_ros.md) for the fake of this node used in sim.

### Publishes

- `/odom_can` - Normal Odom message, but with only velocity filled with the encoder value off the bus.

### Subscribes

- `/robot/ack_vel` - AckermanDrive command to actuate on the kart.
- `/odom` - Velocity is taken from odom to close the speed control loop. As such, the frequency of this topic will
  control the frequency of the control loop.

### Services Used

- `/robot/set_state` - service on `robot_state_controller`. Sets state to KILL or PAUSE when the estop/pause is
  triggered. This should stop `drive_mode_switch` from accepting any twist, regardless of drive mode.
  ACTIVE is sent
  when estop or pause are undone.

### Structure

This node has three main threads, to fulfill its asynchronous duties:

1. The main ROS node thread, which handles pub sub
2. CAN read, which handles encoder data
3. PID control, which computes and sends commands to can

These three threads communicate in a few ways, which means we have to consider race conditions:

1. The main thread sends odom feedback and new commands to the PID thread, which is synchronised using a concurrent
   queue.
2. The main thread and the PID thread write values to the teensys FD, which is also used for reading on the read thread.
   This is currently not synchronised since it is a full duplex connection, but technically this could be racy.
3. The writes themselves between the PID thread and main are racy, because write is non-atomic. We lock the fd for
   writes to compensate for this, ensuring the whole packet is written.

### PID Algorithm

We control the speed of the bot using two separate PID loops, one for throttle, and one for brakes. Only one of these
should be active at a time. This larger PID loop takes feedback from the Kalman filter, which means that our **control
rate is directly tied to the kalman filter**.

For each update:

- Calc error
- If error is negative and under some threshold
    - Update brake PID
- Else if error is negative
    - Disable all PID loops, and just coast
- Else if error is positive
    - Update throttle PID
- Return the calculated PID value, as well as the actuator to apply it to

Downstream, the code is expected to release the actuator that is not being currently used, and set the one chosen to
the control output.