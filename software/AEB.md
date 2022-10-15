# Automatic Emergency Braking ECU

## Overview
The Automatic Emergency Braking (AEB) system is designed to be a supplemental safety system
for the automatic driving. The goal of AEB is to lower the speed of the kart as much as possible
before a potential collision, to mitigate the damage to both objects. This is necessary as the E2E inference
is non-deterministic, and thus requires additional deterministic safety systems to ensure functional safety.    

## Inputs
- **UART to LD06 LiDAR** - Tiny 2d LiDAR for perception.

- **Perception CAN**
  - **Encoder data** - any sensor that outputs `Encoder Count` frames.
  - **Steering data** - any sensor that outputs `Set Angle` frames.

## Outputs 
- **EStop** - direct connection to Estop circuit to allow for emergency braking.

## Algorithm
The general idea will be to take all Lidar points, constrain them to an angle range, then map those 
points onto a bounded occupancy grid, where each cell is either occupied by an obstacle, or free space.
we will then use the ackermann equations to determine the current path
of the kart, and create an interpolated line along the path of the kart with the width of the kart.
If the kart's path attempts to set a cell high that was already set high by the point clouds, then we have a collision.
Because this grid should be about ourselves, we can take the euclidean distance from that collision to the kart,
and find our time to collision (TTC) by finding our velocity at that point along the curve. If that TTC is too low, then estop.

**General Algo:**
- Given a LiDAR scan within the range (-25, 25) degrees
- Create an `nxn` grid, where n is the number of cells and must be odd, and cells are some real size measurement `m`, defined by `10/n`.
  - This grid will always be 10mx10m in real measurements.
- Define a collision box about the kart `CB`, defined as a linear transform from the center of the rear axel to the top left and bottom right of area of the vehicle that can collide with things
- For each LiDAR point in the scan `p`
  - If `p` is < 150mm away from the LiDAR, skip `p`
  - Get the x,y coordinates of the point relative to the kart by converting from polar to euclidean
  - Apply the linear transform between kart point space and grid space
  - If that x,y point is in an unoccupied cell, then mark it as occupied by obstacle
- Create a function for the arc created by the current motion of the kart, f(t, velocity, wheelbase, ackermann_steering_angle) where velocity, wheelbase, and ackermann_steering_angle
are constants for this iteration of the algorithm, herby referred to as `f(t)`. The values from `f(t)` should be the location of the center of the rear axel of the kart at some time, as well as its heading. (TODO)
- For each `t`; `0 < t <= TTC`; with some step size dependent on `m` (TODO)
  - `kart_x, kart_y, yaw = f(t)`
  - Using `CB`, find the defining points of the box in kart space (make sure to rotate by `yaw`)
  - Apply the linear transform from kart space to grid space
  - Find the cells on the grid that fall under `CB`, and call them `c`. (TODO: rasterisation algo?)
  - For each `c`:
    - if `c` is occupied:
      - Fire estop, then halt
- Clear grid, as it will be recalculated with the next lidar scan

### Kart space to grid space transformation
We define kart space $K$ to be a vector space of $R^2$ whos origin lays on the front of the kart, and whos unit vectors are swapped (ie. +x is forward, +y is rightward). $K$ s origin is located at the center colomb and max row + 1 in the grid.

We define grid space $G$ to be a vector space of $Z^2$ whos origin is at (0, 0) in a 2d array. Visualising this as a grid, (0,0) will be at the top left corner, and (max R, max C) will be in the bottom right corner.

To translate a point from $K$ to $G$, we apply a linear transform:

$$ T: K \rightarrow G,
T (\begin{pmatrix}
R \\
C
\end{pmatrix}) = 
\lfloor
1/m
\begin{pmatrix}
  -1 & 0\\ 
  0 & 1
\end{pmatrix}
*
\begin{pmatrix}
R \\
C
\end{pmatrix}
+
\begin{pmatrix}
n \\
n - 1 \over 2
\end{pmatrix}
\rfloor
\equiv
\begin{pmatrix}
\lfloor n - \frac{R}{m} \rfloor \\
\lfloor \frac{n - 1}{2} + \frac{C}{m} \rfloor
\end{pmatrix}
$$

Where $R$ is the forward component of a kart point, $C$ is the rightwards component of a kart point, and $m$ is the size of a grid square side, in meters.

## Functional Requirements
- REQ1: The ECU will take into account the latest velocity or range estimates available on CAN.
- REQ2: If the time to collision falls under n seconds, then the Estop will be triggered.
- REQ3: The system should continue to function if one of the data sources stop sending data. This may include
a false positive or negative stop.
- REQ4: If the system is activated, an external indicator should be activated to clarify that it was AEB that stopped the 
kart.
- REQ5: The most accurate representation of current steering angle should be read from CAN and stored.

## Non Functional Requirements
- REQ1: The algorithm has a soft time constraint of 100ms (10hz LiDAR updates)
