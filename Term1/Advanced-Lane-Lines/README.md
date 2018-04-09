# Advanced Lane Line Finding

In this project, I would like to build a pipeline on computer vision and deep learning .

The steps of this computer vision part were the following:

* Computation of camera calibration matrix and distortion coefficients from a set of chessboard images
* Apply a distortion correction to raw images.
* Color and Gradient Threshold
* Birds Eye View
* Lane Detection and Fit
* Curvature of Lanes and Vehicle Position with respect to Center
* Warp back and display information
* Sanity check
* Video Processing

## Camera Calibration
We are trying to correct images in which the camera should have captured a straight line but due to optical distortion it comes out curved. In order to perform this correction we need to calculate the camera matrix where we need object and image points. The object points are just checkerboard coordinates while the image points are the detected checkerboard edges. I used `cv2.calibrateCamera()` and `cv2.undistort()` for performing the camera calibration, see my code in `advanced_lane_finding.py`, Lines 14-48! The result for one example image looks like the following:

Original             |  Undistorted
:-------------------------:|:-------------------------:
![](camera_cal/calibration1.jpg?raw=true)  | ![](output_images/undistorted_example1.jpg?raw=true) 

## Thresholded Binary

Now that we estimated the camera matrix for correcting the distortions we can apply these to our images of the road and furthermore use color gradients to form a binary image in which our lane lines should stand out. As there are many parameters to tweak I used the excellent `interact` method from the jupyter notebooks to find the best parameters for seperating lane lines with the different thresholds.

The result for one of the test images would look like this:

Undistorted             |  Threshed
:-------------------------:|:-------------------------:
![](output_images/undistorted_example_road1.jpg?raw=true)  | ![](output_images/threshed_example_road1.jpg?raw=true) 

## Perspective Transformation

We now transform the perspective to a birds eye view for fitting a polynomial to the lane lines. For this we need source and destination points, where the `src points` describe a polygon in the original image which will be transformed to a polygon in the `dst points`. I hardcoded the values and used :
```python
src = np.float32([[293, 668], [587, 458], [703, 458], [1028, 668]])
dst = np.float32([[310, im_shape[1]], [310, 0],[950, 0], [950, im_shape[1]]])
```
After perspective transformation the images will loke like this:

Threshed             |  Perspective Transformed
:-------------------------:|:-------------------------:
![](output_images/threshed_example_road1.jpg?raw=true)  | ![](output_images/transformed_example_road1.jpg?raw=true) 

## Fitting a polynomial to the lane
For fitting a polynomial to the perspective transformed and thresholded image we use a sliding histogram approach, where our maximum peaks in the bottom half of the image correspond to the start of the lane. We then subsequently search for the line with the same approach and finally fit a polynomial, which will yield following result:

## Video Production
We now can use this do find lane lines in videos by just performing these procedures on each frame subsequently.

## Discussion
Projecting into birds-eye view seems a very nice method for detecting lane lines. I still think that a more robust method can be applied by using a deep learning model.

Humans can also predict lane lines without seeing them, as they are experienced (for example with snowy roads). This is something where this algorithm would fail but a more robust one could learn to still detect them. 
