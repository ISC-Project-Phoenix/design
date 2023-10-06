# obj_detector

## Summary

This node will detect the objects used to line the track at AKS. This item is TBD. Because of this vagueness, we use
a Pose array as a generic interface for detections.

### Subscribes

- `/camera/mid/rgb` - RGB images
- `/camera/mid/depth` - Depth images
- `/camera/mid/rgb/camera_info` - Camera intrinsics

### Publishes

- `/object_poses` - An array of Poses, representing the centroids of each detected object in free space. Note that these
objects will be in the frame of the camera. 

### Detection Algorithm

While it depends on the exact object we use, I would first attempt to segment the objects into masks, by some classic
CV detection mechanism. Ex. colors combined with shape and reflectivity. Once these are detected, find the center weighted
point of each objects mask. Finally, find the location of that point in the depth image, and use that depth data as the
location of that object. 

We may have to rectify the images with the wide angle lens first, but I'm not 100%.

#### Finding center of each object

Given a mask, I think you can find the center of each object by first finding contours,
then using functions for contours to find the center of each. You may also be able to
do some filtering here for objects that are unlike ours in shape or size.

#### Finding object location from pixels

To find the position of the object IRL from a pixel, we use fancy projection math. Ros has a package for this called
image_geometry that will be very useful. This uses CameraInfo messages for metadata. Given these:
1. call `PinholeCameraModel::projectPixelTo3dRay` with the pixel coordinates to get a quaternion rotating a ray towards the pixel
2. convert that quaternion to RPY using TF convert quat to euler
3. use:```
   x = depth * sin(pitch) * cos(yaw);
   y = depth * sin(pitch) * sin(yaw);
   z = depth * cos(pitch);```
   1. Test this, things may be shuffled around because of ROS coordinate systems
4. This point is in the cameras frame, so we want to transform this to base_footprint

