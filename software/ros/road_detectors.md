# road_detectors

## Summary

In the detector there are two methonds for parsing road data from the cameras. We have a CV node which uses tradional 
computer vision techiques and an AI node which uses a custom YOLO 7 model designed for the perdue track. 

This node starts in the CVSubcriber which subcribes to `/camera/mid/rgb/image_color` and gets images from the camera. 
this frames are processed in the image_callback which hands them off to the backend. the backend applys tradional 
computer vision techiques to extract egde data and converts into contours. We run regession on each edge and average 
there coefficients to get the middle polynomial. We return these values and then publish them to the planners. 

The backend applies a bitwise map 
to the top 52% of the frame removing the sky and the ground. 

### Subscribes

- `/camera/mid/rgb/compressed` - compressed RGB images


### Publishes

- `/road/Contours` - publishes the contours (x,y) of the contours and the polynomials which repersent the egdes
  and center of the track, repsectively. Created in mid_link frame

- `/road/polynomial` - publishes the Float32MultiArray of the coefficients of the centered polynomial. 

### Algorithm

The detector makes uitizies its primrary logic in the backend.py file. The backend processes the frame to extract edge data. 
First applied to the image is the ROI bitwise mask. This ingore the Top 52% of the screen to avoid the sky

we then use
1. a gussian blur to remove noise. 
2. Morphological Closing to connect broken lines. 
3. Canny edge detection is ran to find the contours. 

We then take the two biggest contours based on arc length in order to find the Left and right lines.

The backend returns coefficients for the left and right edge polynomials, and the contour data for the left and right edges that were detected. 

We vertify these edges through there relative arc length, why taking the difference of the points closest and farrest from the origin and check to see if this exceedes our threshold. If so, we do not publish any data, otherwause we publish both the /road/Contours and the /road/polynomials for backwards compatablity. 