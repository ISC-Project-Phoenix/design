# Phoenix_gazebo

This package contains the launch files for running phoenix in its gazebo simulation environment. Overall, this package
should attempt to be a virtual version of [phoenix_robot](phoenix_robot.md) as close as possible.

there are three main launch files:
- prod.launch.py: Runs the production version of phoenix
- data_collect.launch.py: Runs phoenix in data collection mode
- common.launch.py: Launch file that launches nodes common between the above two files (including teleop)

## Ros Config

Red = common.launch.py

Black = prod.launch.py

Blue = data_collect.launch.py

```mermaid
stateDiagram-v2
    classDef common color:white,fill:red
    classDef data color:white,fill:blue
    classDef prod color:white,fill:black

    %% Hardware interfaces
    Gazebo --> gz_bridge:::common
    gz_bridge:::common --> Gazebo
    gz_bridge:::common --> data_logger:::data: /camera/mid/rgb

    
    %% command controllers
    drive_mode_switch:::common --> gz_io_ros:::common: /robot/ack_vel
    gz_io_ros:::common --> gz_bridge:::common: /robot/cmd_vel
    logi_g29:::common --> drive_mode_switch:::common: /ack_vel

    %% logging
    gz_io_ros:::common --> data_logger:::data: /odom_ack
    data_logger:::data --> disk: CSVs and images

    %% Gazebo sensors
    gz_bridge:::common --> kalman:::common: /camera/mid/imu
    gz_bridge:::common --> kalman:::common: /odom_can
    kalman:::common --> gz_io_ros:::common: /odom

    gz_bridge:::common --> obj_detector:::common: /camera/mid/rgb
    obj_detector:::common --> obj_tracker:::common: /object_poses

    kalman:::common --> obj_tracker:::common: /odom
    obj_tracker:::common --> kalman:::common: /odom_tracker
    obj_tracker:::common --> obj_planner:::common: /tracks

    obj_planner:::common --> hybrid_pp:::common: /path
    hybrid_pp:::common --> drive_mode_switch: /nav_ack_vel

    gz_bridge:::common --> hybrid_pp:::common: /scan
```
