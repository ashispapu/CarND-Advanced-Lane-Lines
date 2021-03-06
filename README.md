
[//]: # (Image References)
[distortion]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/distortion_11.png
[distortion_theory]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/distortion_11.png
[corners_unwarp]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/corners_unwarp2.png
[distortion_corrected]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/undistorted.png
[sobel_x]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/sobel_x.png
[sobel_y]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/sobel_y.png
[gradient_magnitude]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/gradient_magnitude.png
[gradient_direction]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/gradient_direction.png 
[color_thresholds]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/color_thresholds.png 
[multiple_thresholds]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/thresholded_binary.png
[region_masked]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/region_masked.png
[perspective_transform]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/perspective_transform.png
[sliding_windows]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/sliding_windows.png
[shaded_lanes]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/shaded_lanes.png
[lane_mapping]: https://github.com/ashispapu/CarND-Advanced-Lane-Lines/blob/master/output_images/lane_mapping.png



## Udacity's Self-Driving Car Nanodegree Program
### Project 4 - Advanced Lane Finding   

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position. 

Let's get started ...  

---
In this project we apply different computer vision algorithms using opencv to perform advanced lane detection . First
we apply on test images  and then the same pipeline is being applied to video stream .

Below is the detail description of all different techniques .

###Camera Calibration 

The code for this step is contained in Sections 1 & 2 of P4_Advance_Lane_Finding.ipynb.

![alt text][distortion]

First, I define "object points", which represent the (x, y, z) coordinates of the chessboard corners in the world. I assume that the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` is appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` is appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

![alt text][distortion_theory]

From there, I used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][corners_unwarp]

Here are the results of distortion correction on each of the test images (located in the test_images folder):

![alt text][distortion_corrected]

###Pipeline (Images)

 Complete pipeline for thhis project is given below 

![ScreenShot](./project_pipeline.png)

Image Source : https://medium.com/@heratypaul/udacity-sdcnd-advanced-lane-finding-45012da5ca7d#.lmyp49h7u

![alt text][multiple_thresholds]

The example above displays the results of multiple thresholds on each test image in the test_images folder. Section 3 of the code explains this process step-by-step, with examples of each individual threshold. In order, the thresholding used on the images is as follows:

+ Color Channel HLS & HSV Thresholding - I extract the S-channel of the original image in HLS format and combine the result with the extracted V-channel of the original image in HSV format.

![alt text][color_thresholds]  

+ Binary X & Y - I use Sobel operators to filter the original image for the strongest gradients in both the x-direction and the y-direction.

![alt text][sobel_x]
![alt text][sobel_y] 

The region masking results are shown below. I chose to limit the mask to a region with the following points: 

| Point       | Value                                    | 
|:-----------:|:----------------------------------------:| 
| Upper Left  | (image width x 0.4, image height x 0.65) | 
| Upper Right | (image width x 0.6, image height x 0.65) |
| Lower Right | (image width, image height)              |
| Lower Left  | (0, image height)                        |

![alt text][region_masked]

The code for my perspective transform is performed in a function  called transform_perspective (Section 4). The function takes in a thresholded binary image with the source points coinciding with the region masking points explained in the region masking table above. For destination points, I chose the outline of the image being transformed. Here are the results of the transforms:

![alt text][perspective_transform]  

The next step after transforming the perspective was to detect lane-line pixels and to fit their positions using a polynomial in Section 5 of my code. After developing functions for sliding_windows and shaded_lanes, I was able to detect the lanes and yield the following results:

Sliding Windows Technique:
![alt text][sliding_windows]

Shaded Lanes Technique:
![alt text][shaded_lanes] 

After detecting the lanes I needed to calculate the radius of curvature for each of the polynomial fits that I performed. The results of these calculations are shown in the table below. I used the radius of curvature example code from Udacity's lessons to create the calculation cells.

| Test Image | Radius of Curvature (Left) | Radius of Curvature (Right) | 
|:----------:|:--------------------------:|:---------------------------:| 
| test1.png  | 2985.467894 meters         | 2850.142018 meters          | 
| test2.png  | 4984.505982 meters         | 12357.329365 meters         |
| test3.png  | 10088.084712 meters        | 2363.421967 meters          |
| test4.png  | 9894.520013 meters         | 2366.846436 meters          |
| test5.png  | 2548.327638 meters         | 6124.849321 meters          |
| test6.png  | 4173.313472 meters         | 45794.832663 meters         |

Another calculation performed was the offset from the lane's center. The calculations are shown in the code cell following the radius of curvature, and yielded the following:

| Test Image | Offset from Center |
|:----------:|:------------------:| 
| test1.png  | 1.020143 meters    |
| test2.png  | 0.901214 meters    |
| test3.png  | 0.935571 meters    |
| test4.png  | 1.123214 meters    |
| test5.png  | 0.930286 meters    |
| test6.png  | 1.070357 meters    |  

Finally, I plotted the warped images back down onto the road such that, for each image, the lane area is identified clearly:

![alt text][lane_mapping]


### Pipeline (Video)

Click on the image or link below to view the video results of the project.

### P4_video_final.mp4
https://www.youtube.com/watch?v=cqasdvt4zhc  
 

<a href="https://www.youtube.com/watch?v=cqasdvt4zhc&feature=youtu.be
" target="_blank"><img src="http://img.youtube.com/vi/5ZKbpNY-rok/0.jpg" 
alt="YouTube" width="240" height="180" border="10" /></a>

### 

### Discussion
The project is bit challenging in the sense that organizing all the functional units to a complete system . First few steps like camera calibration , finding the  gradient magnitude and direction and color thresholding are straight forward .The  level of difficulties slowly increase with perspective tranform  and making it work on the video stream .This has lot to do with the tweaking all the different thresholds and more trails on test images . What I feel , my pipline won't be able to perform at this level if videos were taken at night . For this I need to work more on different scenarios to make it more robust . Overall , It's a good experience to work on this project .




