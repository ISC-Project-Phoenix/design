# gz_io_ros

## Summary

This node is responsible for translating gazebo odom messages into TrainingData messages when training in sim.

Its goal is to serve as a fake for [phnx_io_ros](phnx_io_ros.md).

### Subscribes

- `/odom` - Gazebo odom source
- `/robot/cmd_vel` - Steering and throttle command twist source

Sync the above two topics.

### Publishes

- `/odom_ack` - AckermannDrive messages representing the current state of the kart. See module readme for more info.

### Conversion Algorithm

Converting the odom to training data will amount to converting the twist to ackermann steering and storing that in the steering field,
then copying the twist's velocity_x into the acceleration field, and finally copying the odom's velocity_x into the velocity field.