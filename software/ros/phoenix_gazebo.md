# Phoenix_gazebo

This package contains the launch files for running phoenix in its simulation environments. Overall, this package
should attempt to be a virtual version of [phoenix_robot](phoenix_robot.md) as close as possible.

there are two main launch files:

- prod.launch.py: Runs the production version of phoenix
- common.launch.py: Launch file that launches nodes common between the above two files (including teleop)


## Ros Config

Red = common.launch.py

Black = prod.launch.py

Blue = data_collect.launch.py

Note that webots_ros2_driver will appear in rqt as /Phoenix

```mermaid
stateDiagram-v2
    classDef common color: white, fill: red
    classDef data color: white, fill: blue
    classDef prod color: white, fill: black
%% Hardware interfaces
    Webots --> webots_ros2_driver:::common
    webots_ros2_driver:::common --> Webots
%% command controllers
    drive_mode_switch:::common --> webots_ros2_driver:::common: /robot/ack_vel
    logi_g29:::common --> drive_mode_switch:::common: /ack_vel
%% Gazebo sensors
    webots_ros2_driver:::common --> kalman:::common: /camera/left/imu
    webots_ros2_driver:::common --> kalman:::common: /camera/right/imu
    webots_ros2_driver:::common --> kalman:::common: /odom_can
    webots_ros2_driver:::common --> obj_detector1:::common: /camera/left/rgb/image_color
    webots_ros2_driver:::common --> obj_detector1:::common: /camera/left/rgb/camera_info
    webots_ros2_driver:::common --> obj_detector2:::common: /camera/right/rgb/image_color
    webots_ros2_driver:::common --> obj_detector2:::common: /camera/right/rgb/camera_info
    obj_detector2:::common --> detection_cat:::common: /object_poses/right
    obj_detector1:::common --> detection_cat:::common: /object_poses/left
    detection_cat:::common --> obj_planner:::common: /object_poses
    kalman:::common --> hybrid_pp:::common: /odom
    obj_planner:::common --> hybrid_pp:::common: /path
    hybrid_pp:::common --> drive_mode_switch: /nav_ack_vel
    webots_ros2_driver:::common --> hybrid_pp:::common: /scan
```

#### webots setup

First, install webots. Inside the phoenix_gazebo package, there is a webots_project folder. This folder contains the
files for webots. The go-kart track world contains the physics config and structure of the world. This file can be
edited
and saved in webots without ROS entirely, which is an efficient way to update the world. This world imports two Protos (
basically urdfs for webots), one for the bot itself and the other for cones. Phoenix's proto is adapted from its urdf
file,
but only loosely. It inherits from webots Car proto, which enables it to use powertrain simulation and the driver API,
which makes wb_io_ros significantly easier to implement. The webots_ros2_driver reads tags from the urdf to bridge some
drivers over, however these devices must also be in the proto. Because of this, _both_ the urdf and proto must be
updated when a sensor location or setup changes. This is because the proto will handle the sim sensor location, and the
urdf will handle the location of that sensors ros frame, so any split between these two will lead to error.
