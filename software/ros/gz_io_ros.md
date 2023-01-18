# gz_io_ros

## Summary

This node is responsible for translating gazebo odom messages into TrainingData messages when training in sim.

Its goal is to serve as a fake for [phnx_io_ros](phnx_io_ros.md).

### Subscribes

- `/odom` - Gazebo odom source
- `/robot/cmd_vel` - Steering and throttle command twist source

Sync the above two topics.

### Publishes

- `/training_data` - AckermannDrive messages representing the current state of the kart.
  These messages contain steering in the steering field, throttle/brake in the acceleration feild (m/s), and current kart velocity
  in the velocity feild as (m/s).

### Conversion Algorithm

Converting the odom to training data will amount to converting the twist to ackermann steering and storing that in the steering feild,
then copying the twist's velocity_x into the acceleration feild, and finally copying the odom's velocity_x into the velocity feild.