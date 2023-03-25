# Phoenix_gazebo

This package contains the launch files for running phoenix in its gazebo simulation environment. Overall, this package
should attempt to be a virtual version of [phoenix_robot](phoenix_robot.md) as close as possible.

there are three main launch files:
- inference.launch.py: Runs the production version of phoenix, using the NN inference to drive the kart
- data_collect.launch.py: Runs phoenix in data collection mode, labeling images for offline training
- common.launch.py: Launch file that launches nodes common between the above two files

## Ros Config

Red = common.launch.py

Black = inference.launch.py

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
    gz_io_ros:::common --> gz_bridge:::common: /robot/steering_angle
    logi_g29:::common --> drive_mode_switch:::common: /ack_vel

    %% logging
    gz_io_ros:::common --> data_logger:::data: /odom_ack
    data_logger:::data --> disk: CSVs and images

    %% NN
    gz_io_ros:::common --> inference:::prod: /odom_ack
    inference:::prod --> drive_mode_switch:::common: /nav_ack_vel
    gz_bridge:::common --> inference:::prod: /camera/mid/rgb

    gz_bridge:::common --> gz_io_ros:::common: /odom
```