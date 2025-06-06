# Hybrid Pure Pursuit

## Summary

Path tracking implementation of the pure pursuit algorithm used to command velocity and Ackerman angle to navigate to calculated points along a selected path. 

### Subscribes

- `/odom`: Odometry, this is used to get the linear velocity to determine a look ahead distance.
- `/path`: This is the path that is being supplied from the polynomial planner node, should be a list points offset to the middle of the lane from the left side of the track

### Publishes

- `/nav_ack_vel`:  The calculated ackerman angle and velocity to be set to phoenix.
- `visualization_marker`: The look ahead distance, path arc prediction, and interpolated path to be plotted on rviz for
  live visualization of the path predictions.

## Params

- `min_look_ahead_distance`: The minimum look ahead distance to intersect the path and obtain a path point that is
  kinematically possible for phoenix.
- `max_look_ahead_distance`: The maximum look ahead distance to intersect the path and obtain a path point that is
  within the max range of the camera.
- `k_dd`: A constant multiplier used to scale lookahead based upon current speed.
- `rear_axle_frame`: A string used for the odom to rear_axle transformation.
- `max_speed`:  Max possible speed of phoenix in meters per second.
- `wheel_base`: The wheelbase of phoenix in meters.
- `avoidance_radius`: The minimum distance the path can be to any laser scan.
- `gravity_constant`: The constant acceleration due to gravity.
- `debug`: Debug flag to determine if we want to publish the visualization markers.

### Algorithm

Currently we take in the path from polynomial planner and create a spline from the list of points. Then based off our current
speed we calculate the look ahead distance which is used to check the point of intersection infront of us along the
spline. That point of intersection then becomes our target point to which we calculate our steering angle and command
velocity. Upon reciving the path message, we manipulate each point to move around laser scans creating a curve around
obstacles.

#### Velocity determination

By the power of physics 1, we can determine the max speed for a turn with:
`v = sqrt(static_friction * gravity * turn_radius)`
For this, we will need to calculate the static friction of our tires. After consulting combustion, a value of 1 should
be accurate.
