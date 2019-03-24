# **Finding Lane Lines on the Road** 
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />

Overview
---

When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

In this project, I have implemented algorithms to detect lane lines in images using Python and OpenCV. OpenCV means "Open-Source Computer Vision", which is a package that has many useful tools for analyzing images.  



## Working example of Lane detection
![Alt Text](https://github.com/unnat5/CarND-LaneLines-P1/blob/master/laneDetection.gif)




## Pipeline 
- This pipeline describes steps taken to detect lane lines.
- The main challenge in this project was to how to detect/construct a continuous lane line when the given or marked lane line are dashed.
- First, we change the images to grayscale (same with videos too as they are a collection of images)
- This step helps us to find gradients between different pixels.
- But before calculating gradient we need to smooth the image, so we use `cv2.GaussianBlur()` for smoothing of the image as in it finds the average in given kernel size.
- With the help OpenCV library and using its function `cv2.Canny()` we find different dots which satisfies our threshold values for gradients and which comes under our _quadilateral mask_.
- With those edges with the help of `cv2.HoughLinesP()` we draw lines on the image. This function uses a concept like Hough Transform which is just conversion from image space to Hough space.
- And in Hough space, we can represent an image in terms of parameters.
- And while doing so we change our coordinate system to polar coordinate, as the convention system has the problem with vertical lines and how to define its slope.
- After finetuning the parameter we can get a fairly good lane detection algorithm, which output will look something like this on an image:
 <img src="examples/line-segments-example.jpg" width="380" alt="Combined Image" />
- But the desired output is this:
 <img src="examples/laneLines_thirdPass.jpg" width="380" alt="Combined Image" />
 
- So how to get the above desired image after processing. The main issue here is that, the desired output needs continuous lane line. But on our roads we have dashed lane lines, so main problem here is to first detect the lane lines and which could be of many different colors like white, yellow and etc. 
- And we need to find a way to extropolate that dashed lines to a continuous single.
- So that we can represent or find the drivable area for our self driving car.
- One solution to solve this could be that we could fit a line along these detected points which are fairly separated and then extend the edges to form a single continuous line.
- So in my implementation what I've done is collected the pair of points(returned from `cv2.HoughLinesP()`)  and then checked it's the slope and accordingly put it into left line or right line list.
- And after collecting all the point with the help of `np.poliyfit()` I have calculated the best fit line parameters for both left and right lane lines.
- And then extended the line from two extreme points from the collected points for both right and left lane lines with help of `cv2.drawline()`.


### Future Work and Possible improvements
- One of the problems which I am facing is this that my same pipeline(or algorithm) should work for different type lanes on which I'm driving it could be right, left or centre lane.
- So keeping this in my mind my area of interest which is defined by a quadrilateral and it's used as a mask when using CANNY algorithm is **quite large** in this implementation.
- Due to this large area of interest in my CANNY algorithm, it picks outlier points too.
- Due to these *outlier points* when I'm calculating the parameters for the best-fit line it introduces the error in my calculation and these polyfit algorithms are *very sensitive to outliers*.
- Future work will be how to exclude and remove these outliers from the calculation while approximating the parameters for the best fit line.
