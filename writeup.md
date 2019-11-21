## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[image1]: ./output_images/camera/calibration/calibration3_orig.jpg "Original image"
[image2]: ./output_images/camera/calibration/calibration3_calibration.jpg "Calibration image"
[image3]: ./output_images/camera/correction/calibration3_orig.jpg "Original image"
[image4]: ./output_images/camera/correction/calibration3_calibrated.jpg" "Calibrated image"
[image5]: ./output_images/pipeline/straight_lines1_orig.jpg "Original image"
[image6]: ./output_images/pipeline/straight_lines1_undistorted.jpg "Undistorted image"
[image7]: ./output_images/pipeline/straight_lines1_warp.jpg "Bird-eye view"
[image8]: ./output_images/pipeline/straight_lines1_binary.jpg "Bird-eye view binary Sobel and HLS"
[image9]: ./output_images/pipeline/straight_lines1_undistorted.jpg "Undistorted image"
[image10]: ./output_images/pipeline/straight_lines1_warp.jpg "Warped image"
[image11]: ./output_images/pipeline/straight_lines1_proc.jpg "Lane processing image"
[image12]: ./output_images/pipeline/straight_lines1_output.jpg "Final output image image"
[video1]: ./project_video.mp4 "Video"
[video2]: ./project_video_debug_out.mp4 "Debug Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The notebook associated with this step can be found in the "project_files/Camera Calibration.ipynb" file.

The steps for calibration ar the following:

* Cell 1:
1. Create two lists "objpoints" and "imgpoints" that will hold the index pattern and the associated (x, y) in the image respectively
2. Identify the chessboard corners in the grayscale image
3. Add the identified points to the list of imgpoints and their associated indexes to objpoints whenever the detection was successful
The original image can be 
![alt text][image1]
The identified pattern can be observed below:
![alt text][image2]

* Cell 2:
4. Apply the parameters obtained from the calibration to the original image
The Camera calibration can be observed below:
![alt text][image3]
The identified pattern can be observed below:
![alt text][image4]



### Pipeline (single images)

The notebook associated with this step can be found in the "project_files/Pipeline_final.ipynb" file.
#### 1. Provide an example of a distortion-corrected image.

Using the calibration done in the Camera.calibrate() method the input file:
![alt text][image5]
was corrected to:
![alt text][image6]
It can be observed that the checkerboard lines are now straight.

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

The code that provides the binaryimage can be found in the Pipeline.binaryFy() method.
The steps are the following:
1. The image is converted to grayscale
2. The sobel filter is applied and then scaled to a maximum value of 255
3. A binary image is created just from the Sobel filter by checking the generated values to a pair of low and high thresholds
4. The second part of the processing is done by converting to another colorspace: HLS
5. Only the Saturation component of the HLS color space is used.
6. Another binary image is created from the Saturation component by checking the generated values to another pair of low and high thresholds
7. Finally both the binary images obtained from steps 3. and 6. are combined(overlapped) into a single binary image

The input image is:
![alt text][image7]

The output imgage is:
![alt text][image8]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The transfomation of the image to a "bird-eye view" can be found in the Pipeline.warpImage() method and uses the information provided by the Camera class. First the original image is undistorted then the transformation matrix and the inverse transformation matrixes are generated. With the obtained matrix the transformation to a "bird-eye view" can be obtained.

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 720,  450     | 1200, 100     | 
| 1350, 700     | 1200, 700     |
| 50,   700     | 200,  700     |
| 565,  450     | 200,  100     |

A sample of the conversion can be observed in the following:
![alt text][image9]
![alt text][image10]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Two metods are used:
1. Pipeline.findLaneLinesHist() - When no information of the lane lines is available.


2. Pipeline.findLaneLinesAround() - When the lines were previously detected and approximated by a polynomial for each lane. The detection is done around the polinomial curve in this case.

![alt text][image11]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The curvature is calculated in the Pipeline.calculateCurvature() method.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The last part of the processing refers to drawing the polygon overlaying the deteted road:
![alt text][image12]

---

### Pipeline (video)

The notebook associated with this step can be found in the "project_files/Pipeline_final.ipynb" file.
#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_videos/project_video.mp4)
Here's a debug output [link to my video result](./output_videos/project_video_debug_out.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The first issue encountered was choosing optimal values for the threshold parameters; both for the Sobel filter and color transform.
While the image processing pipeline creation was fast the same parameters did not work in some cases which required firther twealing.
To debug the behavior both a multiple display video was created for the different stages of the pipeline as well as reducing the processed video to a subset of frames where processing did not work correctly.
The pipeline may not work correctly in cases when even small objects or potholes may introduce confusion in the histogram that will lead to an incorrect detection of the lane lines.
Trying to fit a second order polynomial may also not work when multiple curves are encountered in a short distance. A higher order polynomial may work better instead.
Using the Saturation part of the HLS space the yellow line could be distinguished better.
Because the camera is shaking the curvature and off-center values vary most of the time.