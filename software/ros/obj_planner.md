# obj_planner

## Summary

This node plans a path through the center of tracked points. The general idea is to use the tracks as bounds of the track,
and so stay on track by creating paths that consist of points at the midpoint between tracks on alternating sides of the track.

A benefit of this approach is that planning is local and very fast, which allows it to be used without localisation.

### Subscribes

- `/tracks` - Tracked objects. Note that these are in some frame that is attached to base_link.

### Publishes

- `/path` - a nav_msgs Path to follow. These will be in our odom frame.

### Algorithm

This planner will likely have a huge amount of edge cases, such as when the sides are not balanced. As well, simply finding
"The point on the other side of the track" needs to be expressed mathematically, which requires some finnese.
In addition, the algorithm should only replan when a new tracks ID is published. This should help with the case
where a bunch of tracks are lost because we lose side of part of the track, but we still want to keep moving forward
regardless. Once the tracks come back in the form of new ids, we replan.

Rough general planning Algo:
- Transform all tracks into base_link (relative to base of kart)
- Segment points into right and left side by the fact that points to the right of the kart in base_link will be 
-y and to the left of the kart will be +y
- Sort both sides by ascending x distance 
- Create a dictionary of left tracks to right tracks
- For each left track
  - For each right track
    - If the delta between the left x and right x is under some threshold, add left->right track into the map
    - If the left track was already a key, then replace the value only if the new right track x is closer than the old one
- Order the map by ascending left x again
- for each map entry, find the midpoint of the two points
  - Transform this midpoint from base_link->odom (freeze the points so when the kart moves the points don't)
  - Save this point as a Pose in an array
- Publish the pose array as a Path

Now the above algorithm will not handle corners, so it needs some work. Regardless, I think this is a strong starting 
point. Consider for example clustering the points by x distance, then differentiating left vs right relative to only
points in the same cluster.

