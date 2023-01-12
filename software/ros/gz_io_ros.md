# gz_io_ros

## Summary

This node is responsible for translating gazebo odom messages into TrainingData messages when training in sim.

Its goal is to serve as a fake for [phnx_io_ros](phnx_io_ros.md).

### Subscribes

- `/odom` - Gazebo odom source (or a real full odom source)

### Publishes

- `/training_data` - Training data translated from odom

### Conversion Algorithm

Converting the odom to training data will amount to essentially running twist to ackermann on the odom message to
extract the steering angle, as well as finding the throttle percent via diving velocity by max speed.