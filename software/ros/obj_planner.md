# obj_planner

## Summary

This node will create a vector of points in the center of the track for the kart to follow. 
There will be two methods for planning that match the two methods in the obj_detector. 
The CV method:
The AI method:

A benefit of this approach is that planning is local and very fast, which allows it to be used without localisation.

### Subscribes

- `/obj_poses` - If CV: A vector of integers that represents a 2D polynomial in real space. 
                 If AI: A masked image of the lane

### Publishes

- `/path` - a nav_msgs Path to follow. These will be in our odom frame.

### Algorithmj

The Planner can be split into 2 parts, the CV and AI algorithms held in separete folders and are selected based on the subscribed "obj_poses" data. 

CV:
1. 
2. 
3. 

AI:
1. 
2. 
3. 




Our backend implementation is considerably simpler. It simply takes these right left sets, and creates
a path by pairing them and finding midpoints.

1. Sort all points by X axis, as the frontend does not deliver them in order
2. Find the side of the road with fewer points
3. For each point on the smaller side
    1. Find the closest point on the other side of the road
    2. Save these two points as a pair
4. For each pair
    1. Find the midpoint between these two points
    2. Append this midpoint to the path as a target point
5. Transform the path into odom