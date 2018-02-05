# Advanced Lane Finding

## Udacity Self Driving Car Engineer Nanodegree - Project 4


The goal of this project is to develop a pipeline to process a video stream from a forward-facing camera mounted on the front of a car, and output an annotated video which identifies:
- Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
- Apply a distortion correction to raw images.
- Use color transforms, gradients, etc., to create a thresholded binary image.
- Apply a perspective transform to rectify binary image ("birds-eye view").
- Detect lane pixels and fit to find the lane boundary.
- Determine the curvature of the lane and vehicle position with respect to center.
- Warp the detected lane boundaries back onto the original image.
- Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.


### Step 1: Distortion Correction
The first step in the project is to remove any distortion from the images by calculating the camera calibration matrix and distortion coefficients using a series of images of a chessboard.

![Corners Image](./images/cornersshowed.png)

Next, the locations of the chessboard corners were used as input to the OpenCV function `calibrateCamera` to compute the camera calibration matrix and distortion coefficients. 

Finally, the camera calibration matrix and distortion coefficients were used with the OpenCV function `undistort` to remove distortion from highway driving images.

![Undistorted Image](./images/undistortedshowed.png)

### Step 2: Perspective Transform
The goal of this step is to transform the undistorted image to a "birds eye view" of the road which focuses only on the lane lines and displays them in such a way that they appear to be relatively parallel to eachother (as opposed to the converging lines you would normally see). To achieve the perspective transformation I first applied the OpenCV functions `getPerspectiveTransform` and `warpPerspective` which take a matrix of four source points on the undistorted image and remaps them to four destination points on the warped image. The source and destination points were selected manually by visualizing the locations of the lane lines on a series of test images.

![Birds Eye Image](./images/warpedshowed.png)

### Step 3: Apply Binary Thresholds
In this step I attempted to convert the warped image to different color spaces and create binary thresholded images which highlight only the lane lines and ignore everything else. 
I found that the following color channels and thresholds did a good job of identifying the lane lines in the provided test images:
- The S Channel from the HLS color space, with a min threshold of 180 and a max threshold of 255, did a fairly good job of identifying both the white and yellow lane lines, but did not pick up 100% of the pixels in either one, and had a tendency to get distracted by shadows on the road.
- The L Channel from the LUV color space, with a min threshold of 225 and a max threshold of 255, did an almost perfect job of picking up the white lane lines, but completely ignored the yellow lines.

![Binary Thresholds](./images/thresholds.png)

### Steps 4, 5 and 6: Fitting a polynomial to the lane lines, calculating vehicle position and radius of curvature:

### Step 7: Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.
The final step in processing the images was to plot the polynomials on to the warped image, fill the space between the polynomials to highlight the lane that the car is in, use another perspective trasformation to unwarp the image from birds eye back to its original perspective, and print the distance from center and radius of curvature on to the final annotated image.

![Filled Image](./images/filled.png)

## Video Processing Pipeline:
After establishing a pipeline to process still images, the final step was to expand the pipeline to process videos frame-by-frame, to simulate what it would be like to process an image stream in real time on an actual vehicle. 


|Project Video|Challenge Video|
|-------------|-------------|
|![Final Result Gif](./images/project_vid.gif)|![Challenge Video](./images/challenge.gif)|

#### Discussion

##### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

In this project I followed the recomendations given by Udacity. The trickiest part was to create a reusable python module. It was also hard to implement sliding window technique and its visualization.

The implementation performs well on the project video and not so well on other videos with worse environment conditions. The pipeline will likely fail in the situations when the road has cracks coming alongside the lane lines, such as in challenge video. Sanity checks and recovery are helping here---eventually the lanes are detected correctly, but there are noticable sequence of frames on the chanllenge video, when lines are detected completely wrong. Just for the interest of the reader, here is lane detection on chalenge video: [YouTube](https://youtu.be/s0q61dsZPHM). 

Smothing and failure recovery should be more sophisticated to make `AdvancedLaneFinder` work with videos capturing worse environment conditions. Lane detection can be improved with the follwoing approach: obtain lane pixels by color. The rationale behind it is that lanes in this project are either yellow or white. 