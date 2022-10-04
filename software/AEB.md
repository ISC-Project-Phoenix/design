# Automatic Emergency Braking ECU

## Overview
The Automatic Emergency Braking (AEB) system is designed to be a supplemental safety system
for the automatic driving. The goal of AEB is to lower the speed of the kart as much as possible
before a potential collision, to mitigate the damage to both objects. This is necessary as the E2E inference
is non-deterministic, and thus requires additional deterministic safety systems to ensure functional safety.    

## Inputs
- **Perception CAN**
  - **UART to LD06 LiDAR** - Tiny 2d LiDAR for perception.
  - **Encoder data** - any sensor that outputs `Encoder Count` frames.

## Outputs 
- **EStop** - direct connection to Estop circuit to allow for emergency braking.

## Algorithm:
The general idea will be to take all Lidar points, constrain them to an angle range, then map those 
points onto a bounded voxel grid, where each voxel is either occupied by an obstacle, free space, or will be occupied by the kart. 
we will then use the ackermann equations to determine the current path
of the kart, and create an interpolated line along the path of the kart with the width of the kart.
If the kart's path attempts to set a voxel high that was already set high by the point clouds, then we have a collision.
Because this grid should be about ourselves, we can take the euclidean distance from that collision to the kart,
and find our time to collision (TTC) by finding our velocity at that point along the curve. If that TTC is too low, then estop.

- Create an `nxn` grid, where n is the number of voxels, and voxels are some real size measurement `m`.
- For each LiDAR point in the scan `p`
  - Get the x,y coordinates of the point relative to the kart by converting from polar to euclidean
  - If that x,y point is in an unoccupied cell, then mark it as occupied by obstacle (TODO is this trival?)
- Use the configured wheelbase, steering angle, ect. to create a function for the arc created by the current motion of the kart, herby referred to as `f(t)`
- For each `t`; `0 < t <= some max timestep`
  - `kart_x, kart_y = f(t)`
  - Mark that cell containing `kart_x, kart_y` as occupied by kart, call that cell `C`
  - Mark `c` cells to the left and right of `C` as occupied by the kart, where `c = kart_width/(m*2)`
  - If any of those `c` cells attempt to write to a cell that is already occupied by an obstacle (ignoring cells occupied by the kart):
    - Calculate our time to collision as: `TTC = t` (alternatively, TODO)`TTC = dist(kart_x, kart_y)/kart_vel`, where dist is some distance function from the origin to kart_x, kart_y
    - If `TTC` > `Tolerance`:
      - Fire estop, and exit
- Clear grid, as it will be recalculated with the next lidar scan

## Functional Requirements
- REQ1: The ECU will take into account the latest velocity or range estimates available on CAN.
- REQ2: If the time to collision falls under n seconds, then the Estop will be triggered.
- REQ3: The system should continue to function if one of the data sources stop sending data. This may include
a false positive or negative stop.
- REQ4: If the system is activated, an external indicator should be activated to clarify that it was AEB that stopped the 
kart.