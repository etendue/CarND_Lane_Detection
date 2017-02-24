

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[orig]: ./test_images/solidYellowCurve.jpg "Original Image"
[grayscale]: ./pipeline/solidYellowCurve_grayscale.png "Gray Scale"
[gaussian]: ./pipeline/solidYellowCurve_gaussian.png "Gaussian Blur"
[canny]: ./pipeline/solidYellowCurve_canny.png "Canny Edge Detection"
[region]:./pipeline/solidYellowCurve_region.png "Region of Interest"
[hough]:./pipeline/solidYellowCurve_hough.png "Hough Line Selection"
[out]: ./pipeline/solidYellowCurve_out.png "Final Output"
[horizontal]: ./Horizontal.jpg "Horizontal line"

---

### Reflection



### 1. The pipelines follows the process introduced in course. It consists 6 steps. 
orignal image

![alt text][orig]

1. convert RGB image to grayscale image 
![alt text][grayscale]

2. do gaussian blur to smooth image 
![alt text][gaussian]

3. do Canny edge dectection
![alt text][canny]

4. confine the interested region by clipping out pixels outside a polygon 
![alt text][region]

5. do Hough lines selection to filter out lines not fulfilling criteria, e.g. length
![alt text][hough]

6. combine the lane lines produced from previous step with original image
![alt text][out]

In Hough line selection step, the draw_lines function is modified. Instead to draw the lines return by cv2.HoughLinesP function, the lines are extrapolated by 

1. dividing lines into group for left lane and group of right lane. The grouping criteria is slope of lines, i.e. 
  * a group of lines with positive slopes
  - a group of lines with negative slopes
  
2. do polynomial fit (here only linear fit) on points in each segmentation. Points are derived from lines

3. calculate the two end points of each lane. These points are
  * top points, the y coordinate is derived from interested region, x coordinate is extrapolated by polynominal fit 
  - bottom points, the y coordinate is image height, x coordinate is extraplated by polynominal fit



### 2. Identify potential shortcomings with your current pipeline

There are several potential shortcomings:
- the region of interest is hard coded. This will change on roads with slopes
- the lane lines are assumed straight lines
- the contrast of lanes are assumed high enough so that edge detection can filtering out noise


#  !!!*Update from first review*
The parameters for Hough Line selection is optimized, 
originally they were set very small

##  Before
*threshold = 5*
*minimalLenght= 20*
*maximumGap = 10*

## After
*threshold = 80*
*minimalLenght= 80*
*maximumGap = 50*

- I realized that I did not understand the meaning of these parameters, so the result was bad. But by increasing these values, short lines are filtered out.

- Besides the Draw_lines are also optimized to remove lines with slope ~ 0. Here is example which caused the abnomality with my old code.
![alt text][Horizontal]

- the threshold can not be set too large, if too large e.g. >120, the segmented lane will be filtered out



### 3. Suggest possible improvements to your pipeline

A possible improvement regarding to potential shortcomings would be:

- Following information can be used to determine region of interest
  * detect the front boundary between road and surroundings e.g. sky, mountain. Roads have patterns to detect
  - the slope of road. It can be measured by gyro sensor in car or get from map.

- If lane is not straight e.g. at road at corner or a curved highway, the draw_lines method can be modified to :
  * instead poly fit a line, polyfit a curve.
  - interpolate the curve between two end points
  
- the pineline does not work properly for challenge video. I guess the edge detection does not work properly due to the change of road texture, lighting etc.  My ideas are:
  * adjust the edge detection parameters and hough line selections
  - average the hough lines from previous image with current image, this can smooth the change
  - (speculation only )use more sophiscated technologies e.g. lighting model analysis, machine learning,etc


