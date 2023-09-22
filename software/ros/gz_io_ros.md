# gz_io_ros

## Summary

This node is responsible for aggregating ackermann commands and gazebo odom values together into values used in
data logging. 

It is also the endpoint in the system for ackermannDrive commands, being converted to twist commands for gazebo itself.

Its goal is to serve as a fake for [phnx_io_ros](phnx_io_ros.md).

### Subscribes

- `/odom` - Gazebo odom source
- `/robot/ack_vel` - Steering and throttle command ackermannDrive source

Sync the above two topics.

### Publishes

- `/odom_ack` - AckermannDrive messages representing the current state of the kart. See module readme for more info.
- `/robot/cmd_vel` - Twist messages to control the drive part of sim actuation.

### Notes

On receiving a command, the node should both create the odom and forward the command to gazebo.
