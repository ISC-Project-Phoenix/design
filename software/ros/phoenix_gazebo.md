# Phoenix_gazebo

This package contains the launch files for running phoenix in its gazebo simulation environment. Overall, this package
should attempt to be a virtual version of [phoenix_robot](phoenix_robot.md) as close as possible.

there are three main launch files:
- inference.py.launch: Runs the production version of phoenix, using the NN inference to drive the kart
- data_collect.py.launch: Runs phoenix in data collection mode, labeling images for offline training
- common.py.launch: Launch file that launches nodes common between the above two files

## Ros Config

Red = common.py.launch

Black = inference.py.launch

Blue = common.py.launch

```mermaid
stateDiagram-v2
    classDef common color:white,fill:red
    classDef data color:white,fill:blue
    classDef prod color:white,fill:black

    %% Hardware interfaces
    Gazebo --> gz_bridge:::common
    gz_bridge:::common --> Gazebo
    gz_bridge:::common --> data_logger:::data: /camera/mid/rgb
    gz_bridge:::common --> gz_io_ros:::common: /odom
    
    %% command controllers
    drive_mode_switch:::common --> gz_bridge:::common: /robot/cmd_vel
    drive_mode_switch:::common --> gz_io_ros:::common: /robot/cmd_vel
    joy_to_teleop_twist:::common --> drive_mode_switch:::common: /cmd_vel

    %% logging
    gz_io_ros:::common --> data_logger:::data: /odom_ack
    data_logger:::data --> disk: CSVs and images

    %% NN
    gz_io_ros:::common --> inference:::prod: /odom_ack
    inference:::prod --> drive_mode_switch:::common: /nav_vel
    gz_bridge:::common --> inference:::prod: /camera/mid/rgb
```