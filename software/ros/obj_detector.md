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

The detector is split into two halves: a frontend, and a backend. The frontend takes an RGB image and finds the
center points of objects using masking, morphology, contours, and more. The Backend then takes these center points
and then finds the real world (x, y, z) position of the object using camera intrinsics and depth images.

This two stage design allows us to provide many kinds of object detectors by just changing out the frontend, and
reusing the rest of the infrastructure. This is critical to allowing us to test with cones or markers for comp.

#### Masking

TODO

#### Finding center of each object

TODO

#### Finding object location from pixels

To recover the 3D position of the object W.R.T. The camera, we can utilize the camera intrinsics alongside depth data:

1. Project object center pixel from image to pixel space using $K^{-1}$. This gives us a ray pointing in the direction
   of the object in camera space
2. Find the depth of the object by checking that pixel in the depth image
3. If depth is $> 10m$ (over sensor max) or $<= 0$, skip this object
4. By treating the ray as a vector, we can find the point in camera space by simply adjusting the magnitude of the ray
   to the depth. This is done with: $point = \frac{depth}{\left \| ray \right \|} * ray$.
5. Project this point to "World" space (really just WRT the camera) by rotating it from camera space coordinates to ROS
   coordinates. This is done by rotating roll $-pi/2$ and yaw $-pi/2$.

This algorithm works very well, but is vulnerable to noise in the depth data. This will need to be filtered by tracking
further down the pipeline.
