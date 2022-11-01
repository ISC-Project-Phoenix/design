# State Machine

Phoenix's high level state machine contains all the conceptual states the kart can enter.
This state machine is accurate at a high level, but may not be explicitly stored in every subsystem.


```mermaid
stateDiagram-v2
    [*] --> Teleop: Fully Launched
    Manual --> Teleop: PC Break Off
    Teleop --> Manual: PC Disconnects
    Teleop --> Auton: ROS Joy Toggle
    Auton --> Manual: PC Disconnects
    Auton --> Teleop: ROS Joy Toggle
    Auton --> Manual: Pedal Input
    Auton --> Manual: PC Break On
    Teleop --> Manual: Pedal Input
    Teleop --> Manual: PC Break On

    [*] --> Training: Training mode launched
```