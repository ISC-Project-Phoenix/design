# Phnx_io_ros AKA Can interface

## Summary

Bridges ROS to the CAN network. Our single point of actuation.

This node also contains our speed control loop (essentially cruse control). Basically, this node will take command
messages in `/robot/ack_vel` which contain both desired steering angle (which is handled on the bus) and a desired
velocity.
This node will then pass this set speed to a control loop, which will run in a background thread. This loop will be
ticked
by odom to close the loop, producing both throttle and brake commands on the bus as needed to meet the set speed.

Currently, this node also connects to the roboteq motor controller directly, bypassing the bus. Messages are still sent
on the bus, however they are not currently used for throttle.

This node is also the source of estop state in ROS, reacting to detected state changes from the bus and cycling both its
own state (stopping the speed loop), and also signaling robot state controller, and thus the rest of the system. It
also handles unestopping, which involves synchronizing the system with hardware and state transitioning.

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

The speed of the bot is currently controlled with just the motor, although the brake is available for future work.
This is done in a simple PID loop, ticking at the speed of odom. This loop must be set to 0 whenever the kart is killed,
else integral windup will lead to the kart blasting off when enabled.

### Roboteq

A connection is made to the roboteq motor controller automatically by searching /dev/serial/by-id. This means that
the controller just needs to be connected over a direct usb connection, but an RS232 adapter should also work. Our
interface to the motor controller is quite simple, primarily just sending blind power commands from the speed control
loop. 