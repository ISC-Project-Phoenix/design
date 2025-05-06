# polynomial_planner

## Summary

This node plans a path by sampling points into the center of the track. The general idea is to model the bounds of the tracks 
as contours, and treat them as bounds for which we shouldn't cross over. 

A benefit of this approach is that planning is local and very fast, which allows it to be used without localisation.

### Subscribes

- `/road/Contours` - publishes the contours (x,y) of the contours and the polynomials which repersent the egdes and center of the track, repsectively. Created in mid_link frame
- `/camera/mid/rgb/camera_info` - publishes the intresentic values of the camera, including its matrix.

### Publishes

- `/path` - a nav_msgs Path to follow. These will be in our odom frame.

### Algorithm

The planner works its magic in the backend, we take the polynomials and differentiate at points on a fixed interial. The dx/dy 
is treated as a vector and its Orthogonal complements are generated and scaled accoridng. 'TBD' whether or not these will account 
for the dept of in camera space or on a fixed scaler in ROS space.


Our backend implementation is considerably simpler. It simply takes these right left sets, and creates
a path by pairing them and finding midpoints.

1. Validate each of the contours is not null
2. Loop through the left contour
    1. Shift the point left by 2ish meters.
