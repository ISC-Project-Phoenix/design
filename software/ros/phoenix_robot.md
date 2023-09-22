# phoenix_robot

This package contains launch files for running phoenix IRL.

there are three main launch files:

- prod.launch.py: Runs the production version of phoenix
- data_collect.launch.py: Runs phoenix in data collection mode, configuring data_logger alongside prod. (TODO impl)
- common.launch.py: Launch file that launches nodes common between the above two files
- ultilibot.launch.py: Launch file used on a remote computer networked to the kart. Launches
teleop nodes.

## Ros Config

Red = common.launch.py

Black = prod.launch.py

Purple = utilibot.launch.py

Blue = common.launch.py

```mermaid
stateDiagram-v2
    classDef common color:white,fill:red
    classDef data color:white,fill:blue
    classDef prod color:white,fill:black
    classDef util color:white,fill:purple

    %% Hardware interfaces
    oak_d:::common --> data_logger:::data: /camera/mid/rgb
    Can --> phnx_io_ros:::common: Encoder, Estop state
    phnx_io_ros:::common --> Can: Actuation commands
    
    %% command controllers
    drive_mode_switch:::common --> phnx_io_ros:::common: /robot/ack_vel
    logi_g29:::util --> drive_mode_switch:::common: /ack_vel
    
    %% state control
    phnx_io_ros:::common --> robot_state_controller:::common: /robot/set_state
    robot_state_controller:::common --> drive_mode_switch:::common: /robot/state

    %% logging
    phnx_io_ros:::common --> data_logger:::data: /odom_ack
    data_logger:::data --> disk: CSVs and images

    %% Localisation
    phnx_io_ros:::common --> kalman:::common: /odom_can
    oak_d:::common --> kalman:::common: /camera/mid/imu

    %% new stuff
    oak_d:::common --> obj_detector:::common: /camera/mid/rgb
    obj_detector:::common --> obj_tracker:::common: /object_poses

    kalman:::common --> obj_tracker:::common: /odom
    obj_tracker:::common --> kalman:::common: /odom_tracker
    obj_tracker:::common --> obj_planner:::common: /tracks

    obj_planner:::common --> hybrid_pp:::common: /path
    hybrid_pp:::common --> drive_mode_switch: /nav_ack_vel

    isc_sick:::common --> hybrid_pp:::common: /scan

    kalman:::common --> phnx_io_ros: /odom
```
