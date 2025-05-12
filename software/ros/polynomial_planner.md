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

The planner works its magic in the backend, we take the contours and iterate through on a fixed interial. We then offset on a fixed number anf fixed interval. Initally we used polynomials to model the road bounds, but this wwas dropped after poor modeling of the bounds due to poor fitting. The raw camera (x, y) was sent to the planner instead. We then differentiate at points on a fixed interial. The dx/dy is treated as a vector and its Orthogonal complements are generated and scaled accoridng, however this was impractical to do with camera coowardinates. Instead, we used a fixed interval without differentiating since this would have the secondary effect of cutting corners, which was considered ideal behavour for the path as this would be a a techically faster path. For future teams however, there is room for improvement than just happenstance planning. An important behivour to keep in mind is that the pure pursuit node will also cut sharper corners, so test and refine your model with the full stack. 


The shorten impletation of the planner

Implementation is surprising effective for how simple it is. 

It simply takes left contour sets, and creates
a path by offsetting them on a fixed interval. 

1. Validate each of the contours is not null
2. Loop through the left contour
    1. Shift the point left by 2ish meters.
    note that your offset affects how much you will cut on sharper corners, so a proper safety margin is in order. 
