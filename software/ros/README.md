This folder contains documents detailing the design of Project Phoenix's
Ros components.

## Overview

| package                             | use                                                                            |
|-------------------------------------|--------------------------------------------------------------------------------|
| [phoenix_robot](phoenix_robot.md)   | Running phoenix IRL, for racing or data collection                             |
| [phoenix_gazebo](phoenix_gazebo.md) | Running a virtual version of phoenix in gazebo, for testing or data collection |

### On the use of AckermannDrive messages

We use AckermannDrive messages throughout ROS to funnel vehicle state data around. Inside of these messages, values that
will ultimately need to be percents (such as steering angle, throttle, and brake) are represented using exact angles
and speeds respectively. The reasoning for this is that we have to primarily support data collection via telop, who's
nodes will send twist messages. These twist messages represent throttle as a velocity and braking as a negative
velocity.
As a result of this, nodes at the edge will need to store a max_throttle_speed or similar parameters in order to convert
speed back to percents. It is very important these values are consistent across all nodes including joy inputs, else
NN output will be biased. (TODO update)

To be clear on the format:

**When ackermann messages are being used as odom:**

- `steering_angle` - Steering angle of the **wheel**, not ackermann wheel. Positive yaw is to the left, in rad.
- `speed` - Velocity the kart is actually moving at, from encoders or gazebo odom.
- `accelleration` - Represents current throttle/brake position as a speed, where a negative speed represents braking.
  This is done as controllers output speed, not percents. (TODO just make 0-1?)
  We leave it up to end nodes to convert back to percents if needed.
    - _Note that it is not possible to represent the brake and throttle being pressed simultaneously._ This is inherent
      from the same constraint
      in twist messages. This shouldn't be in issue in our case, since brake boosting an electric go-kart is silly.

**When ackermann messages are being used as commands:**

AckermannDrive messages are used as intended when being sent as a command to hardware.