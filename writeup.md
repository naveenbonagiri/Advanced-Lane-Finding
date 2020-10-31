***Write up on "Advanced Lane Finding Project"***

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

[image1]: ./output_images/undistort_output.png "Undistorted"
[image2]: ./output_images/Road_Transformed.jpg "Road Transformed"
[image3]: ./output_images/binary_combo.jpg "Binary Example"
[image4]: ./output_images/warped_image.jpg "Warp Example"
[image5]: ./output_images/color_fit_lines.jpg "Fit Visual"
[image6]: ./output_images/example_output.jpg "Output"
[video1]: ./project_video_processed.mp4 "Video"

All my implementation is located in 'Adv Lane Lanes Finding.ipynb' in the Jupyter workspace my classroom.


### Camera Calibration

Camera calibration is implemented in calibrate_camera function which is cell 2 the implementation file.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: /output_images/undistort_output.png

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
test_images/test5.jpg. Camera focaol lengths, optical centers (camera matrix), distortion coefficients captured in camera calibration process are passed onto cv2.undistort to undistort the image. test5.jpg resulted into undistorted image which is captured in ./output_images/undistort_output.png.

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps using functions `abs_sobel_thresh()`, `mag_thresh()`, `dir_thresh()` and `col_thresh()` in `Adv Lane Lanes Finding.ipynb`).  Thresholding images are combined using function combine_threshs. Here's an example of my output for test5.jpg.

./output_images/test5_binary_combined_image.JPG

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perform_perspective_transform()`, which appears in cell 10 in the file `Adv Lane Lanes Finding.ipynb` The `perform_perspective_transform()` function takes inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 4) - 40, img_size[1] - 20],
    [((img_size[0] / 2) - 45), (img_size[1]/2) + 100],
    [(img_size[0] / 2) + 85, (img_size[1]/2) + 100],
    [((img_size[0] * 7 / 8) + 5), img_size[1] - 20]])
dst = np.float32(
    [[(img_size[0] / 4) - 70, img_size[1]],
    [(img_size[0] / 4) - 70, 0],
    [(img_size[0] * 3 / 4) + 105, 0],
    [(img_size[0] * 3 / 4) + 105, img_size[1]]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 280, 700      | 250, 720      | 
| 595, 460      | 250, 0        |
| 725, 460      | 1065, 0       |
| 1125, 700     | 1065, 720     |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image `straight_lines2.jpg` and its warped counterpart to verify that the lines appear parallel in the warped image as seen in ./output_images/straight_lines2_warped_image.jpg.

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this. It is implemented in function `find_lane_lines()` of `Adv Lane Lanes Finding.ipynb` using following steps:
1. Create a histogram of bottom half image.
2. find the peak of the left and right halves of the histogram. That's going to starting point for lines.
3. identify non-zero pixes in the image
4. step through one window at a time and all windows to indentify lane pixels
5. fit second order polynomial on lane pixes


#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in function `measure_curvature_radius()` in my code in `Adv Lane Lanes Finding.ipynb`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in functions `serach_for_lane_lines() and draw_lines()` in  `Adv Lane Lanes Finding.ipynb`.  Here is an example of my result on a test image straight_lines2.jpg:

./output_images/straight_lines2_lines_drawn.jpg

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Processed video is located in the root workspace with name project_video_processed.mp4

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Ran into issues while creating new windows and searching for new lines based on previoulsy detected lines. This solution is not working well challenege video and harder challenge video. I think src and dst coordinates needs to be tuned well to make it work on those videos. Also, need to test on videos that are captured during less light, night time and check the performance of the solution.
