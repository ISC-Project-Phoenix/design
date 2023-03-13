# phoenix_robot

This package contains launch files for running phoenix IRL.

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
    oak_d:::common --> data_logger:::data: /camera/mid/rgb
    Can --> phnx_io_ros:::common: Encoder, Estop state
    phnx_io_ros:::common --> Can: Actuation commands
    
    %% command controllers
    twist_to_ackermann:::common --> phnx_io_ros:::common: /ack_vel
    drive_mode_switch:::common --> twist_to_ackermann:::common: /robot/cmd_vel
    joy_to_teleop_twist:::common --> drive_mode_switch:::common: /ack_vel
    
    %% state control
    phnx_io_ros:::common --> robot_state_controller:::common: /robot/set_state
    robot_state_controller:::common --> drive_mode_switch:::common: /robot/state

    %% logging
    phnx_io_ros:::common --> data_logger:::data: /odom_ack
    data_logger:::data --> disk: CSVs and images

    %% NN
    inference:::prod --> drive_mode_switch:::common: /nav_vel
    oak_d:::common --> inference:::prod: /camera/mid/rgb
    phnx_io_ros:::common --> inference:::prod: /odom_ack
```