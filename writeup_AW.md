## Writeup
---
**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

---
References:
Udacity starter code provided in the tutorials was used throughout and the Q and A https://www.youtube.com/watch?v=vWY8YUayf9Q&feature=youtu.be was used to help fine tune the approach. 


### Camera Calibration

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()`

![alt text][image1]

### Pipeline (single images)

#### 1.Distortion-corrected image.

Using the distortion coefficients and matrixes that were calculated with the chessboard the following could be used to remove any distortion from the road. 

```python 
undistorted = cv2.undistort(image, mtx, dist, None, mtx)
```

Here is an example of a distortion corrected image of the road. There isn't a big difference between the original image and the undistorted image. This isn't surprising as the camera is a good camera and is not wide angled or fish eye. Although it isn't easy to see the difference, the sign on the right hand side of the picture is closer to the edge. This helps to give a feel for how the edges of the picture are being slightly changed by the distortion. 


![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I explored a number of different ways to generate a binary image. These included x gradient thresholds, y gradient threshold, magnitude gradient thresholds, gradient direction thresholds, R color threshold, V color threshold and S color threshold. I'll explain each one more detail below. 

The starter code for these sections was taken from the earlier sections of the Udacity Tutorial.

x and y gradient thresholds
This approach looks at applying the sobel kernal over either the x or y direction and applying a threshold. In finding locations of large gradient these will help to identify the change from road to line.

Magnitude and Direction thresholds
These approaches look at the overall gradient by combining the x and y gradients to find the overall gradient magnitude and direction. Through experimentation I found the direction approach to be very noisy and neither of these were used in the final implementation.  

R,V,S thresholds
Converting the image between RGB, HLS and HSV allowed me to target different channels. I focused on Red, Saturation and Value. Allowing different thresholds to be applied. 

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines # through # in `another_file.py`).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image3]

#### 3. Perspective transform

For the perspective transform I decided to hard code in the location of the source points and then check these against one of the test images. I then assumed that this would be acceptable for most of the occurances. After having attempted the harder video challenges I chose to limit the distance that the car looks ahead to help manage harder scenarios where there are sharp corners. 

```python 
corner_locations1x = 520.0
corner_locations2x = 770
corner_locations3x = 1050
corner_locations4x = 250

corner_locations1y = 500.0
corner_locations2y = 500
corner_locations3y = 690
corner_locations4y = 690
```

The following image shows where these points would fall on the road. I then projected this onto a standard rectangle and having tried different offsets stuck with 200.

The code for perspective transform is in the section of IPython notebook 'Section 3 - Perspective Transform'

Example of perspective transform on the binary image. 

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
