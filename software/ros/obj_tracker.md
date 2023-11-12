# obj_tracker

## Summary

This node takes arrays of Poses and tracks them over time, assigning IDs. This will be used to track the detections from
obj_detection, both smoothing their output and providing persesitence for when detections flicker in and out.

### Subscribes

- `/object_poses` - Poses from detected objects

### Publishes

- `/tracks` - Tracks generated. Note that these will be in the frame of the poses from `/object_poses`

### Tracking Algorithm

This algorithm will be inspired by existing CV MOT algorithms. Basically, we will have a Kalman filter for each track, where that
Kalman filter has (x,y,z,vx,vy,vz) state variables and (x,y,z) measurement variables. For each cycle, we attempt to pair these
tracks predictions with the detected objects, resulting in new measurements for the kalman filters. Poor fits or unpaired detections
remove or add tracks respectively.

in more detail:
1. If there are no Kalman filters, initialize one for each detection
2. Create set $P_i$ predictions by predicting all kalman filters, where $i$ is the id of the track
3. Create cost matrix $C_ij$, where each $ij$ corresponds to the Euclidean distance between track prediction $P_i$ and detection with index $j$
4. Solve the assignment problem between tracks and points by finding the paring assignment that minimizes the cost (hungarian algorithm)
5. Take the resulting assignment vector $A_i$, where the value of each index contains the $j$ that track $i$ is paired with
   1. Note that, by the assignment problem, all tracks had to be paired with a point, even if not valid.
   2. For non-square matrices, $A_i$ will be -1 for trackers who were not paired to any detection. When there are more detections than trackers, the non-paired detections will simply not appear in $A_i$
6. For each $ij$ pairing where $C_ij > threshold$ or $A_i < 0$, increase the number of missed frames on tracker $i$ by 1. Mark these skipped trackers in set $S$. Note that $threshold$ is the distance value in meters that a track-detection pair must be under in order to be valid, because of 5.1
   1. If tracker $i$'s missed frames is > some missed frames tolerance, then delete the tracker
7. For each $ij$ where $i \not\in S$, correct track $i$'s kalman filter by treating the detection as a measurement
   1. Also set $i$'s missed frames counter to 0
8. For each $j$ not in $A_i$ (new detections), create a new tracker and initialize with the current detection point
9. Publish the state of each tracker (either as just a pose, or with tracking id)


### Kalman Filters

Because we do not measure velocity, the kalman filter will converge on its value over time as the covariance matrix
is updated and impacts state via the kalman gain. Because cones are only present for a brief period of time, we bias
the filters with a negative x velocity on creation to help the track "catch" the cone on its first update, else it may
instantly lose its detection. Once a track catches its detection, the relatively fast update rate of the camera causes
the velocities to converge quickly as corrections roll in.

### References
https://github.com/mithundiddi/hungarian-algorithm-cpp

https://github.com/srianant/kalman_filter_multi_object_tracking

https://towardsdatascience.com/what-i-was-missing-while-using-the-kalman-filter-for-object-tracking-8e4c29f6b795
