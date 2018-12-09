# **Finding Lane Lines on the Road** 

## Writeup

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[img_grayscale]:  ./test_images_output/grayscale.jpg "Grayscale"
[img_blur]:       ./test_images_output/blur.jpg "Blur"
[img_canny]:      ./test_images_output/edge.jpg "Canny"
[img_roi]:        ./test_images_output/roi.jpg "ROI"
[img_hough]:      ./test_images_output/lines.jpg "Hough"
[img_lane_lines]: ./test_images_output/lane_lines.jpg "Lane Lines"

---
### Solution and Results

* The solution can be found in [P1.ipynb](P1.ipynb).
* Sample images with extrapolated lane lines can be found in 'test_images_output'.
* Sample videos with extrapolated lane lines can be found in 'test_videos_output'.

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consiste of the following 6 steps:

#### 1. Grayscaling
First I convert the image into a grayscale image, because we don't need colors for edge detection and it's easier to handle graysacle images with only one dimension instead of color images with three dimensions.

The result looks like this:
![Grayscale][img_grayscale]

#### 2. Gaussian Smooting
The kernal size of the Gaussian filter: The smoothing filter used in the first stage directly affects the results of the Canny algorithm. Smaller filters cause less blurring, and allow detection of small, sharp lines. A larger filter causes more blurring, smearing out the value of a given pixel over a larger area of the image. Larger blurring radii are more useful for detecting larger, smoother edges. 
I use the kernal size 15.

The result looks like this:
![Blur][img_blur]

#### 3. Canny Edge Detection
Next I use the Canny Edge Detection algorithm to detect edges in the image.

The result looks like this:
![Blur][img_canny]

#### 4. Region of Interest Filtering
Assuming that the camera is centered on the front of the car, the road we want to detect in the image is in a triangular area, from the points at the bottom left, to about the center of the image and bottom right. This area is called 'region of interest'. All other areas of the image are not of interest, and therefore I mask out them in black. 

The result looks like this:
![Blur][img_roi]

#### 5. Hough Transform Line Detection
The Canny Edge Detection returned many edges, but we want lines. I use the Hough Line Transform to transform the edges into lines.

I use following parameters for Hough Line Transformation:
* rho: 1
* theta: PI / 180
* threshold: 20
* minLineLength: 20
* maxLineGap: 300

The result looks like this:
![Blur][img_hough]

#### 6. Lane Lines Calculation 
The Hough Line Transformation returned many lines with different slope, intercept and length. But we want to detect only two line lanes, the right and the left one. The idea is to first split all lines into right lines and left lines by their slope. Then we can build the average of the right and left lines, the two averages corresponds to the right and left lane line. 

Finally I extrapolate the detected line lanes from the bottom of the image to the top of the region of interest.

The final result looks like this:
![Blur][img_lane_lines]

### 2. Identify potential shortcomings with your current pipeline
I think the current solution isn't robust enough to work in real life scenarios. There are some questions to clarify and to improve the stability accordingly.

#### Question 1
The current solution uses a fixed region of interest. This works well for the given test images. But all the test images contains flat and relative straight roads. What happens when the road has tight curves or great slopes, like serpentines?

#### Question 2
The test images contains empty roads with 2 clean lane lines and without disturbances, so the current solution works fine with these images. But what happens if the road contains more than 2 lines or some disturbing objects in the region of interest?

#### Question 3
All test images showing roads with the same light and weather conditions. The current parameters for the lane line detection were adapted accordingly to this conditions. But what happens in a wide variety of light and weather conditions such as strong sunshine, darkness, rain, fog, snow, etc.? Can we use only one parameter set for all these conditions?

#### Question 4
What happens if the road has no lane lines at all, or this is e.g. are concealed by snow?

### 3. Suggest possible improvements to your pipeline

To solve problem 1 we could try to adjust the region of interest dynamically to tight curves or great slopes.

To solve problem 3 we could try to use different parameters for different light and weather conditions.

The lane line detection could be encapsulated in a parameterized class, for reuse in different projects.