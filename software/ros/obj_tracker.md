# obj_tracker

## Summary

This node takes arrays of Poses and tracks them over time, assigning IDs. This will be used to track the detections from
obj_detection. During this process, it also derives a visual odometry source by dividing the displacement of IDs between
frames by the time between frames. 

### Subscribes

- `/object_poses` - Poses from detected objects
- `/odom` - Filtered odometry used in state estimation

### Publishes

- `/odom_tracker` - Velocity odom generated in the tracking process
- `/tracks` - Tracks generated. Note that these will be in the frame of the poses from `/object_poses`

### Messages

- `Track`
  - Message of `int ID` and `Pose pose`

### Tracking Algorithm

We should look around and find some existing implementations from opencv.

Generally, the algorithm should look something like the following:

- Set of tracks T
- Input of poses P
- For each Tr in T:
  - Predict the next position of Tr using odom and basic particle motion (could also be kalman filters)
  - Push this to array Tp
- For each Pr in P:
  - Find the closest point in Tp that is still under some threshold distance
  - If such a point exists, update that track in T with the pose of Pr
  - else, allocate a new ID to Pr, and add it as a new track to T
- For each T that was not updated:
  - deallocate the tracks ID, and remove from array

And for odom:
- Given the state of the tracks from the last run
- And the updated tracks from this run
- Find the union of these arrays, by track ID
- For each track in union:
  - sum += euclidean_distance(new_point, old_point)
- avg_displacement = sum / len(union)
- estimated_velocity = avg_displacement / time delta from last run to current run
- Publish that velocity as odom