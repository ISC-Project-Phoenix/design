# obj_detector

## Summary

This node will detect white lane lines on the track at AKS. Detections will be stored as 
2nd degree polynomials in a vector in pixel space and translated to real space from camera space. Using a vector allows for 
different degree polynomials to be used in the future if desired.

### Subscribes

- `/camera/mid/rgb` - RGB images
- `/camera/mid/rgb/camera_info` - Camera intrinsics

### Publishes

- `/object_poses` - A vector of integers that represents a 2D polynomial in real space. 

### Detection Algorithm

The detector is split into two halves: a frontend, and a backend. The frontend takes an RGB image, isloates the 
lane line and creates a vector of elements (3 variables assuming 2nd degree poly) that represent the line as a polynomial, location thresholds and more. 
The backend then takes the sampled points along the lane line and then finds the real world (x, y, z) position of the object using camera instrinsics.

This two stage design allows us to provide different kinds of line detection algorithms or methods by just changing 
out the frontend, and reusing the rest of the infrastructure.

#### Masking

TODO: Method
1. 
2. 

#### Find polynomial
TODO:

#### Finding object location from pixels

TODO

To recover the 3D position of the object W.R.T. The camera, we can utilize the camera intrinsics alongside calculated depth data:

1. Project object center pixel from image to pixel space using $K^{-1}$. This gives us a ray pointing in the direction
   of the object in camera space
2. Find the depth of the object by checking that pixel in the depth image
3. If depth is $> 10m$ (over sensor max) or $<= 0$, skip this object
4. By treating the ray as a vector, we can find the point in camera space by simply adjusting the magnitude of the ray
   to the depth. This is done with: $point = \frac{depth}{\left \| ray \right \|} * ray$.
5. Project this point to "World" space (really just WRT the camera) by rotating it from camera space coordinates to ROS
   coordinates. This is done by rotating roll $-pi/2$ and yaw $-pi/2$.
