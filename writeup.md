--------------------------------------------------------------------------------

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

- Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
- Apply a distortion correction to raw images.
- Use color transforms, gradients, etc., to create a thresholded binary image.
- Apply a perspective transform to rectify binary image ("birds-eye view").
- Detect lane pixels and fit to find the lane boundary.
- Determine the curvature of the lane and vehicle position with respect to center.
- Warp the detected lane boundaries back onto the original image.
- Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

# [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

## Here I will consider the rubric points individually and describe how I addressed each point in my implementation.

--------------------------------------------------------------------------------

## Writeup / README

## Setting up

### 1\. Camera Calibration using `cv2.calibrateCamera()`

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image. Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image. `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection. ![alt text][image0]

### 2\. Undistort an image

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function. I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

![alt text][image1]

## Pipeline (single images)

### 1\. Example image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one: ![alt text][image2]

### 2\. Image processing details

I used a combination of color and gradient thresholds to generate a binary image (6th code cell in `solution.ipynb`). Here's an example of my output for this step.

![alt text][image3]

### 3\. Perspective transformation

I used the API in cv2 : `cv2.warpPerspective()`, which appears in code cell 7. After getting the processed image from `binary_pipeline` (code cell 5) I used the  source (`src`) and destination (`dst`) points to calculate the perspective transform matrix.
- `src` and `dst` array:

 Source   | Destination
:-------: | :---------:
580, 460  |   200,0
710, 460  |   1080,0
1150, 720 |   1080,720
150, 720  |   200,720

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

### 4\. Identified lane-line pixels and fit their positions with a polynomial
Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]
- The `get_pixel_in_window` in code cell 12 is a sliding window that takes image input, x coordinate of the window center, y coordinate of the window center, size of the window in pixel and then returns the coordinate of detected pixels.
- After that, the `histogram_pixels()` in code cell 7 takes the input of wrapped image and used the sliding window above to generate histogram pixels as output.
- Then, the `fit_second_order_poly()` in code cell 10 get the output from `histogram_pixels` to let code cell 12 `draw_poly` to draw the line in different color.


### 5\.  Calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in code cell 12. The final value of the curvature was converted from pixels to meters.

### 6\. Example image of identified clearly lane area.

Here is an example of my result on a test image:

![alt text][image6]

--------------------------------------------------------------------------------

## Pipeline (video)

### After creating the image pipeline, I created a video pipeline in code cell 22 to generate a processed video

Here's a [link to my video result](./project_video_output.mp4)

--------------------------------------------------------------------------------

## Discussion

### Problems and issues
The problem I'm facing is lane detection, moving objects like cars or discontinuous lanes lines may affect the result of recognition.
To solve the issue, I set an area of interests, so that unnecessary objects will be black in the undistorted image. I also increased the minimum threshold to filter out unwanted targets.

### Where will the pipeline likely fail
The pipeline still has the possibility failed to recognize discontinuous lanes

[//]: # "Image References"
[image0]: ./output_images/calibration.png "Undistorted"
[image1]: ./output_images/undistort_output.png "Undistorted"
[image2]: ./output_images/distort.png "Road Transformed"
[image3]: ./output_images/binary.png "Binary Example"
[image4]: ./output_images/wrap.png "Warp Example"
[image5]: ./output_images/colorfit.png "Fit Visual"
[image6]: ./output_images/output.png "Output"
[video1]: ./project_video_output.mp4 "Video"
