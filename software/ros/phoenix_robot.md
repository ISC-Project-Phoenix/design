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

Blue = common.launch.py

Purple = common.launch.py

```mermaid
stateDiagram-v2
    classDef common color:white,fill:red
    classDef data color:white,fill:blue
    classDef prod color:white,fill:black
    classDef util color:white,fill:purple
    classDef ai color:white,fill:purple
    classDef opencv color:white,fill:blue

    %% Hardware interfaces
    Can --> phnx_io_ros:::common: Encoder, Estop state
    phnx_io_ros:::common --> Can: Actuation commands
    
    %% command controllers
    drive_mode_switch:::common --> phnx_io_ros:::common: /robot/ack_vel
    
    
    %% state control
    phnx_io_ros:::common --> robot_state_controller:::common: /robot/set_state
    robot_state_controller:::common --> drive_mode_switch:::common: /robot/state

    %% Localisation
    phnx_io_ros:::common --> kalman:::common: /odom_can
    oak_d_m:::common --> kalman:::common: /camera/mid/imu
    

    %% new stuff
    oak_d_m:::common --> obj_detector_ai:::ai: /camera/mid/rgb
    oak_d_m:::common --> obj_detector_cv:::ai: /camera/mid/rgb
    oak_d_m:::common --> obj_detector_cv:::opencv: /camera/mid/rgb/camera_info
    oak_d_m:::common --> obj_detector_ai:::opencv: /camera/mid/rgb/camera_info
    obj_detector_ai:::ai --> obj_planner_ai:::ai: /masked_img
    obj_detector_cv:::opencv --> obj_planner_cv:::opencv: /poly

    obj_planner_ai:::ai --> hybrid_pp:::common: /path
    obj_planner_cv:::opencv --> hybrid_pp:::common: /path
    hybrid_pp:::common --> drive_mode_switch: /nav_ack_vel

    isc_sick:::common --> hybrid_pp:::common: /scan

    kalman:::common --> phnx_io_ros: /odom
    kalman:::common --> hybrid_pp: /odom
```
