# Hybrid Pure Pursuit

## Summary

This node implements pure pursuit, combined with basic obstacle avoidance. It should be stateless, and operate
based only on its subscriptions. Additionally, it should tick itself to run at a set frequency, since paths will not
be republished at a high frequency.

### Subscribes

- `/path` - Points to follow
- `/odom` - Speed for use in PP
- `/scan` - LiDAR scans to avoid

### Publishes

- `/nav_ack_vel` - Ackermann commands to send to CAN

### Params

TODO Pure Pursuit things

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
For this, we will need to calculate the static friction of our tires.