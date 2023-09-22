# obj_detector

## Summary

This node will detect the objects used to line the track at AKS. This item is TBD. Because of this vagueness, we use
a Pose array as a generic interface for detections.

### Subscribes

- `/camera/mid/rgb` - Depth images (TODO make this a depth topic)

### Publishes

- `/object_poses` - An array of Poses, representing the centroids of each detected object in free space. Note that these
objects will be in the frame of the camera. 

### Detection Algorithm

While it depends on the exact object we use, I would first attempt to segment the objects into masks, by some classic
CV detection mechanism. Ex. colors combined with shape and reflectivity. Once these are detected, find the center weighted
point of each objects mask. Finally, find the location of that point in the depth image, and use that depth data as the
location of that object. 