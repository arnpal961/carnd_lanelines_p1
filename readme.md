# **Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. 

1. I converted the images to grayscale using helper function grayscale() which uses 
   cv2.cvtColor(img,cv2.COLOR_RGB2GRAY) [Here it used cv2.COLOR_RGB2GRAY because the
   image file is read by matplotlib image reader which reads image in RGB format.]

2. Then I smoothed the grayscale image function using helper function gaussian_blur() which uses
   cv2.GaussianBlur() . Here I fixed my kernel at  size 7 . I tested kernel of sizes 3,5,7,9. I 
   found that kernel size of 7 best suits.

3. Then I used canny edge detection technique to detect edges . Here I kept the minimum thresold 
   value at 50 and maximum at 150. I used the helper function canny() which uses cv2.Canny() for 
   edge detection.

4. Then I used the region_of_interest() helper function to select the region where the road exisist .
   As all the image size is 540x960, so I used the vertices [(0,540),(457,327),(518,327),(960,540)]
   to selt the region . It is a trapezium.

5. Now to detect lines I used the hough_lines() helper function . hough_lines() function uses 
   probabilistic Hough Transform method by cv2.HoughLinesP() then followed by cv2.lines() function
   for drawing lines. Here the cv2.HoughlinesP() takes five parameters.
   These are ==> rho, theta, threshold, min_line_len, max_line_gap . 
   I used the value rho = 2,theta = 1 degree,thresold = 20,min_line_len = 30,max_line_gap= 100 . 
   In some cases I used values between 40-100 for max_line_gap parameter to join lines .

6. Then I overlap the hough_lines() function output image with original color image using a alpha
   value of 0.8.

[//]: # (Image References)

[image2]: ./test_images/solidWhiteCurve.jpg "Input image "

[//]: # (Image References)
[image3]: ./test_images_output/solidWhiteCurve.jpg "Output image "

   

 
In order to draw a single line on the left and right lanes, I modified the draw_lines() function. 
This function is basically used by hough_lines() function . First I calculated the gradients by
grad = (y2-y1)/(x2-x1). Now if grad value is less than zero then co-ordinates [(x1,y1),(x2,y2)]
corresponds to left line and for grad > 0 corresponds to right . Then I calculate the L2 norm for
each lines in Left lane and Right Lane . In both lanes which line has maximum norm is the most 
prominent line . And I only took those two lines . Then I calculated the X-axis intercept to extend
the lines. Now using these points I used cv2.line() function to draw the most prominent line . Here
I modified some of the hogh_lines() functions parameter to get the best result as possible . 

[//]: # (Image References)
[image4]: ./test_images_output_modified/solidWhiteCurve.jpg "Output image "


---

### 2. Potential shortcomings of current pipeline


These pipe line can only give best results for the project dataset as it is optimized for the given
datasets. As the parameters ar manualy chosen based on the images given it should not work well with 
other lane lines.

Another major shortcoming of the pipeline is - it is not optimized for tree shadow, curved lanes and
sunshine. It works best with white lane lines not with yellow lines.

---


### 3. Possible improvements for pipeline 

For detecting edges in a shadow or sunshine area and yellow lane lines we should use another 
color space such as HSL space or use individual components of RGB and HLS space .

Only R image works best for detecting yellow lane lines and but can't remove shadows.

In the other hand in L(HLS) space it can detect yellow lane lines and eliminate the effect
of shadow moderately. And also there is no need to take all the X-Y plane below for lane 
lines detection as it includes more obstacles. We can crop the region of interest by modifying
the vertices . 

---

### 4. Working with video data

For applying the pipeline fuction to a video stream first I imported the VideoFileClip class
from moviepy.editor module . Then I instantiated the class with the video file given . Then I 
used the fl_image() method to write my pipe line function to the video stream .

---

### 5. Challenge Video using improvements of the pipeline function
Now for the challenge I modified my pipeline .
Here I considered two cases .

      1. I used only L-space(HLS) image to canny() function.
      2. Then I modified the vertices to narrow the region of interest.

Results are not quite satisfactory but the improvements must be noticed.

