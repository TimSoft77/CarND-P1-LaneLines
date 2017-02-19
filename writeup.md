#**Finding Lane Lines on the Road - Reflection** 


### Project Goals:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

[//]: # (Image References)

[image1]: ./test_images/processed_test_images/whiteCarLaneSwitch-masked.png "Mask Region"
[image2]: ./test_images/processed_test_images/whiteCarLaneSwitch.png "Finished image"


###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

1. Grayscale

2. Find canny edges.  Gaussian smoothing was omitted because I believe cv2.Canny() applies 5x5 Gaussian internally, and I lacked a reason to change it.

3. Apply a mask, as below.  The image below masks an original image, but at this step in the pipeline it would be applied to the canny edges.

  ![alt text][image1]
  
4. Find the Hough lines.  Both min_line_len and max_line_gap were set to 100.  The idea was to only capture long straight lines,  and gap the spaces between dotted lines with cv2.HoughLinesP().

5. Sort the lines into right_lane_lines and left_lane_lines.  Left lane lines are assumed to be those with a negative slope and vice versa.  This is done in draw_lines().

6. For both the left and right lane lines, find the line with the median slope, and extrapolate it to the vertical bounds of the masked region.  This is done in a new function I created called find_best_line().
  * The median was used instead of mean to reduce sensitivity to outliers.

7. Draw the best right and left lane lines on the original image.  A thickness of 10 was used.  One final result is shown below.

  ![alt text][image2]


###2. Identify potential shortcomings with your current pipeline

1. Does not work for curved lines.

2. Will fall apart if the car turns slightly, because then both left and right lanes will have either a positive or negative slope, rendering one side invisible.

3. Will fail if a line segment from cv2.HoughLinesP() had an infinite slope, but this could easily be handled.


###3. Suggest possible improvements to your pipeline

1. Use results from previous images to determine the correct lane lines in the current image.

2. Reject any line outright that does not pass through the bottom of the image (even if extrapolated).

3. I suspect an approach that would reduce the impact of outliers and handle curved lines would be:
  * In cv.HoughLinesP(), use smaller values for both min_line_len and max_line_gap.  This would capture the segments separately, and,  hopefully break curved lines into a series of segments.
  * Use a clustering algorithm to break up the left and right lane lines.
  * For each cluster, use regression analysis on all the lines' endpoints to produce curves.
