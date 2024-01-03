# Hybrid Pure Pursuit

## Summary

Path tracking implementation of the pure pursuit algorithm used to command velocity and ackerman angle to navigate to calculated points along a selected path. The hybrid section
will come into play with the use of the lidar and a TBD obstacle avoidance algorithm. This is a statless implementation and works
only off its subscriptions.

### Subscribes

- `/odom`: Odometry, this is used to get the linear velocity to determine a look ahead distance.
- `/path`: This is the path that is being supplied from the obj_planner node, should be a list of midpoints between respective left and right cones.

- `/scan` - LiDAR scans to avoid `Still TODO`

### Publishes

- `/nav_ack_vel`:  The calculated ackerman angle and velocity to be set to phoenix.
- `visualization_marker`: The look ahead distance, path arc prediction, and interpolated path to be plotted on rviz for live visualization of the path predictions.

## Params

- `min_look_ahead_distance`: The minimum look ahead distance to intersect the path and obtain a path point that is kinematically possible for phoenix.
- `max_look_ahead_distance`: The maximum look ahead distance to intersect the path and obtain a path point that is within the max range of the camera.
- `k_dd`: A constant multiplier used to scale the speed in the calculation for look ahead distance.
- `rear_axle_frame`: A string used for the odom to rear_axle transformation.
- `max_speed`:  Max possible speed of phoenix in meters per second.
- `wheel_base`: The wheelbase of phoenix in meters.
- `gravity_constant`: The constant acceleration due to gravity.
- `debug`: Debug flag to determine if we want to publish the visualization markers.

### Algorithm

Implement traditional pure pursuit as
in https://thomasfermi.github.io/Algorithms-for-Automated-Driving/Control/PurePursuit.html
. For each command this produces, run the [AEB Algorithm](../embed/AEB.md) with this command to see if we will collide
with a scan. If this occurs, then switch to an alternative algorithm to move around the object (TBD, tinykart based?).
We don't store this
in state, rather this process is done for each tick, so we will always use the correct approach at any given time.

#### Velocity determination

By the power of physics 1, we can determine the max speed for a turn with:
`v = sqrt(static_friction * gravity * turn_radius)`
For this, we will need to calculate the static friction of our tires. After consulting combustion, a value of 1 should be accurate.
