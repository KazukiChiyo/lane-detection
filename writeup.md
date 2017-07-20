# **Finding Lane Lines on the Road**
---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[orig]: ./test_images/solidYellowCurve2.jpg "input image"
[filter]: ./filter_images/solidYellowCurve2.jpg "color filter"
[canny]: ./canny_images/solidYellowCurve2.jpg "canny edges"
[roi]: ./roi_images/solidYellowCurve2.jpg "ROI"
[output]: ./output_images/solidYellowCurve2.jpg "output image"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

After receiving the test image (or a frame of image from a video clip) a color filter that filters out anything expect for white and yellow is applied. Specifically, the filter uses a RBG color thresholds for white color and HLS color thresholds for yellow color:
```python
# color threshold for white and yellow
white_lo = [100, 100, 200]
white_hi = [255, 255, 255]
yellow_lo = [20, 120, 80]
yellow_hi = [45, 200, 255]
```
For example, this is a sample result after the color filter is applied:

![alt text][filter]

Now it is possible to convert the image into grayscale and apply a Gaussian filter to blur the image, a preparation step for Canny Edges Detection. After Canny Edges Detection the image looks like this:

![alt text][canny]

Our region of interest is only the trapezoid area to the center bottom of the image, so after masking the image with this region of interest, only the contour of two lines are visible:

![alt text][roi]

After Hough Transform, the canny edges are interpreted as voted discrete x-y coordinates and it is crucial to turn these scattered points into two lines. We adopt linear regression technique and classify the points into two groups, `left_x, left_y` and `right_x, right_y`, determined by their x-coordinate. This enables linear regression line fit on the two categories:

```python
left_slope, left_int = np.polyfit(left_x, left_y, 1)
right_slope, right_int = np.polyfit(right_x, right_y, 1)
```
Furthermore, a quasi-memorization mechanism makes smooth rendering possible. Four global variables, `SAVED_LB, SAVED_LT, SAVED_RB, SAVED_RT`, memories and updates the weighted x-coordinates of 4 endpoints so far with a weight of 0.5. After all processes are done, the output image has two marked lane lines on the original input image:

![alt text][output]


### 2. Identify potential shortcomings with your current pipeline
* The pipeline has problem identifying curves, since HSL and RGB filters does not return a robust output with curved lines, and the linear regression technique does not approximate curved lines.
* Not possible to dynamically adjust region of interest


### 3. Suggest possible improvements to your pipeline
* Possible improvements to the pipeline involves approximating curved lines and dynamically readjust region of interest, which can be accomplished with the aid of machine learning.
