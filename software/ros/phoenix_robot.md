# phoenix_robot

This package contains launch files for running phoenix IRL.

there are three main launch files:

- inference.launch.py: Runs the production version of phoenix, using the NN inference to drive the kart
- data_collect.launch.py: Runs phoenix in data collection mode, labeling images for offline training
- common.launch.py: Launch file that launches nodes common between the above two files

## Ros Config

Red = common.launch.py

Black = inference.launch.py

Blue = common.launch.py

```mermaid
stateDiagram-v2
    classDef common color:white,fill:red
    classDef data color:white,fill:blue
    classDef prod color:white,fill:black

    %% Hardware interfaces
    oak_d:::common --> data_logger:::data: /camera/mid/rgb
    Can --> phnx_io_ros:::common: Encoder, Estop state
    phnx_io_ros:::common --> Can: Actuation commands
    
    %% command controllers
    drive_mode_switch:::common --> phnx_io_ros:::common: /robot/ack_vel
    logi_g29:::common --> drive_mode_switch:::common: /ack_vel
    
    %% state control
    phnx_io_ros:::common --> robot_state_controller:::common: /robot/set_state
    robot_state_controller:::common --> drive_mode_switch:::common: /robot/state

    %% logging
    phnx_io_ros:::common --> data_logger:::data: /odom_ack
    data_logger:::data --> disk: CSVs and images

    %% NN
    inference:::prod --> drive_mode_switch:::common: /nav_ack_vel
    oak_d:::common --> inference:::prod: /camera/mid/rgb
    phnx_io_ros:::common --> inference:::prod: /odom_ack
```