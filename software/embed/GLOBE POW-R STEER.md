# Steering Control

## Overview

Steer by wire is achieved using an electric power steering unit known as the _GLOBE POW-R STEER®
Electric
Power Assisted Steering_. This unit for our purposes is essentially a giant
servo that controls the front steering rack. It is directly connected to CAN,
so no other ESC is necessary.

## Can message

For our purposes, we can just use Command Mode Position, which
will take the motor rotations specified in variable 1 and hold that position.
The docs refer to this as being in units of "Revolutions", but it is unclear what this 
is based off of.
![](images/Steering%20motor%201.png)