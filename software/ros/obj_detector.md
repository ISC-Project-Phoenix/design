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
center points of objects using masking, morphology, contours, pixel thresholds and more. The Backend then takes these center points
and then finds the real world (x, y, z) position of jthe object using camera intrinsics and depth images.

This two stage design allows us to provide many kinds of object detectors by just changing out the frontend, and
reusing the rest of the infrastructure. This is critical to allowing us to test with cones or markers for comp.

#### Masking

1. Lighten the shadows on the RGB image using gamma.
2. Converent the gamma corrected RGB image to HSV.
3. Take the HSV image, set an uppper and lower bound to mask orange pixels and apply.
4. Define the morphological variable. Using this variable the dilate operation 
    is then used to emphasize masked objects. 

#### Finding center of each object

1. Create contours of orange objects in the mask using the findContours OpenCV operation
2. Set a pixel threshold param to reject small masked contours from returing its center 
    if's pixel count is less than the set value.
3. Set a dimensions param to rejects masked contours if the aspect ratio and ap ratio 
    (area_perimeter_ratio) of an object is within a user set range.
5. Find image moments then calculate the center of each contour.
6. Center point is returned into a vector if the contour is not rejected by params.

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
