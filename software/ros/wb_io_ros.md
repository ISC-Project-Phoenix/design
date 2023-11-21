# wb_io_ros

## Summary

This node is actually not a node, but rather a plugin for webots_ros2_driver. This plugin adds functionality to 
webots equivalent to phnx_io_ros and gz_io_ros. Specifically, it publishes sim encoder values as odom, and 
actuates the vehicle in sim based off of AckermannDrive messages.

### Subscribes

- `/robot/ack_vel` - Steering and throttle command ackermannDrive source

Sync the above two topics.

### Publishes

- `/odom_can` - Encoder values from sim in the linear x velocity field
