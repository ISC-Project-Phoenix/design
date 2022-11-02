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

Collision detection will be done on an occupancy grid, where that occupancy grid will be 10m x 10m in real units, relative to the LiDAR. 
This occupancy grid will have a variable resolution. Range point readings will be fed into the grid, marking obstacles about the kart.
To predict where the kart will be at some time for collision detection, we have integrated the ackermann forward kinematics equations over time,
which yields indefinite integrals that give us position and heading at any time.

When collision detection is performed, the karts predicted path will be iterated over, and the karts oriented bounding box 
will be checked against the occupancy grid. If the boxes ever overlay, we have a collision at that time. This will be done until
our max allowed time to collision, so if we have any collision we know it is one that the estop should be fired over.

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
- For each `t`; `0 < t <= TTC`; with 10ms steps
  - `kart_x, kart_y, yaw = f(t)`, where `f(t)` is defined by Kinematics Function
  - Using `CB`, find the defining points of the box in kart space, then rotate them by `yaw`
  - Translate all points by the linear translation from the LiDAR to the center of the rear axel 
  - Apply the linear transform from kart space to grid space
  - Find the cells on the grid that fall under `CB`, and call them `c`. See Rasterization Algorithm for details
  - For each `c`:
    - if `c` is occupied:
      - Fire estop, then halt
- Clear grid, as it will be recalculated with the next lidar scan

### Rasterization Algorithm
The following is an algorithm to find the points bound by a polygon on a grid, either for collision checking or drawing.
Essentially all we do is draw the edges first, record the beginning and end indexes drawn in each row, then go through
and fill in the ranges between all the start and end points.

- Given a list of vertices forming lines
- Create an array of size `N`, which holds structures of (start, end)
- for each line:
  - Use the midpoint line drawing algorithm to find the indexes filled by the line
  - for each index:
    - (x, y) = index
    - if array[x] has been initialized:
      - if start == y:
        - continue
      - if y < start:
        - if end is null:
          - end = start
        - start = y
      - elif end is null or end < y:
        - end = y
    - elif x < N:
      - Create a new record array[x] = (start, null)
    - // Here we visit each point on the edges
    - if !(x < 0 or x >= N or y < 0 or y >= N):
      - visit index, either to collision check or draw

// Now we visit each point inside the polygon
- for (x, record) in array:
  - if record is not null:
    - if end is not null:
      - for y in start..end:
        - if !(x >= N or y >= N):
          - visit cell


### Kinematics Function
We implement the forward kinematic function as the normal forward kinematic equations for ackermann steering integrated 
over time. This way `f(t)` is asking where the position of the kart is after t seconds.

Formally:

$$x = \frac{sin(l * s * \phi * t)}{\phi}$$

$$y = \frac{l*(-cos(\frac{s * \phi * t}{l}) + 1)}{\phi}$$

$$\theta = \frac{s * t * tan(\phi)}{l}$$

where $l$ is the wheelbase in m, $s$ is the speed, $\phi$ is the ackermann virtual wheel angle, $\theta$ is the vehicles
heading, and $(x, y)$ is the position of the vehicles rear axel in the kart frame.

### Kart space to grid space transformation
We define kart space $K$ to be a vector space of $R^2$ whose origin lies on the front of the kart, and who's unit vectors are swapped (ie. +x is forward, +y is rightward). $K$ s origin is located at the center column and max row + 1 in the grid.

We define grid space $G$ to be a vector space of $Z^2$ whose origin is at (0, 0) in a 2d array. Visualising this as a grid, (0,0) will be at the top left corner, and (max R, max C) will be in the bottom right corner.

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
