# obj_planner

## Summary


NEW:


OLD:
This node plans a path through the center of tracked points. The general idea is to use the tracks as bounds of the
track,
and so stay on track by creating paths that consist of points at the midpoint between tracks on alternating sides of the
track.

A benefit of this approach is that planning is local and very fast, which allows it to be used without localisation.

### Subscribes

- `/obj_poses` - 2nd degree polynomails in real space. 

### Publishes

SAME:
- `/path` - a nav_msgs Path to follow. These will be in our odom frame.

### Algorithmj

The planner can largely be split into a frontend and backend. The frontend takes tracks and classifies them as being to
the left or right of the track (harder than it sounds!). The backend takes right-left classifications and creates a
path.

The current frontend implementation is based on the idea of treating tracks as data-points, and drawing a convex hull
around them.

1. Generate convex hull around tracks as points
2. Determine if we are in a left, right, or straight turning scenario
    1. Currently done by a RANASC of the points, finding a line of best fit. The angle of this line
       wrt. the ROS Y axis is then analysed to see if the points trend right or left.
3. If straight, then just trivially classify tracks into left and right via the Y coordinate
4. If turning, then walk the edges of the convex hull until an angle is found that is greater than
   some threshold. This abnormally large angle indicates that we have jumped from the outer edge of the turn to the
   inner edge on the other side of the track.
5. Consider all tracks that are some small distance from the segments traversed to find the outside edge of the turn
   to be our right/left side of the track, depending on the scenario.
    1. Ex. for left turn, these points would be classified as to the right of the track
6. Classify all remaining points as being on the opposite side of the track to step 5

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