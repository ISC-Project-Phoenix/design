# Detection Concatenation

## Summary

Simple node to combine the detections of two object detector nodes. This allows us to use two cameras to increase our 
effective field of view.

### Subscribes

- `/object_poses1` - First stream
- `/object_poses2` - Second stream

### Publishes

- `/object_poses`: Resulting combined detections 
