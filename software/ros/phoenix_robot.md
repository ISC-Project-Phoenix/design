# phoenix_robot

This package contains launch files for running phoenix IRL.

there are two main launch files:

- prod.launch.py: Runs the production version of phoenix
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
    Can --> phnx_io_ros:::common: Encoder, Estop state
    phnx_io_ros:::common --> Can: Actuation commands
    
    %% command controllers
    drive_mode_switch:::common --> phnx_io_ros:::common: /robot/ack_vel
    logi_g29:::util --> drive_mode_switch:::common: /ack_vel
    
    %% state control
    phnx_io_ros:::common --> robot_state_controller:::common: /robot/set_state
    robot_state_controller:::common --> drive_mode_switch:::common: /robot/state

    %% Localisation
    phnx_io_ros:::common --> kalman:::common: /odom_can
    oak_d_l:::common --> kalman:::common: /camera/left/imu
    oak_d_r:::common --> kalman:::common: /camera/right/imu

    %% new stuff
    oak_d_l:::common --> obj_detector1:::common: /camera/left/depth
    oak_d_l:::common --> obj_detector1:::common: /camera/left/rgb
    oak_d_l:::common --> obj_detector1:::common: /camera/left/rgb/camera_info
    obj_detector1:::common --> detection_cat:::common: /object_poses/left
    
    oak_d_r:::common --> obj_detector2:::common: /camera/right/depth
    oak_d_r:::common --> obj_detector2:::common: /camera/right/rgb
    oak_d_r:::common --> obj_detector2:::common: /camera/right/rgb/camera_info
    obj_detector2:::common --> detection_cat:::common: /object_poses/right
    
    detection_cat:::common --> obj_tracker:::common: /object_poses

    obj_tracker:::common --> obj_planner:::common: /tracks

    obj_planner:::common --> hybrid_pp:::common: /path
    hybrid_pp:::common --> drive_mode_switch: /nav_ack_vel

    isc_sick:::common --> hybrid_pp:::common: /scan

    kalman:::common --> phnx_io_ros: /odom
    kalman:::common --> hybrid_pp: /odom
```
