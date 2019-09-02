# **Finding Lane Lines on the Road** 

[//]: # (Image References)

[step_0]: ./screenshot/step_0.png "step_0"
[step_1]: ./screenshot/step_1.png "step_1"
[step_2]: ./screenshot/step_2.png "step_2"
[step_3]: ./screenshot/step_3.png "step_3"
[step_4]: ./screenshot/step_4.png "step_4"
[step_5-1]: ./screenshot/step_5-1.png "step_5-1"
[step_5-2]: ./screenshot/step_5-2.png "step_5-2"
[step_6]: ./screenshot/step_6.png "step_6"

---

### 1. Pipeline description

I use below image as an example to describe my pipeline step by step.

![Step 0][step_0]

#### Step 1: Convert image from RGB to HLS and extract white/yellow colors
Since the color of lane line is either white or yellow. I convert the original RGB image to HLS image first, then use `cv2.inRange` to extract both white and yellow colors.

![Step 1][step_1]

#### Step 2: Gaussian smoothing
In step 2, I use Gaussian blur with `kernel_size = 5` to smooth the HLS image from step 1.

![Step 2][step_2]

#### Step 3: Region of interest
Then using `region_of_interest` to only keep the region of interest. This step can make the following step of finding lane line much more easier.

![Step 3][step_3]

#### Step 4: Canny edge detection
In this step, using canny edge detection technique with parameters `low_threshold = 60` and `high_threshold = 120` to detect the edges.

![Step 4][step_4]

#### Step 5-1: Hough line detection
I choose parameters `rho = 2`, `theta = np.pi/180`, `threshold = 35`, `min_line_len = 22` and `max_line_gap = 40` to run the hough line detection to find the lane lines.

![Step 5-1][step_5-1]

#### Step 5-2: Average and extrapolate the line segments
For the purpose of average and extrapolate the line segments, I need to identify each of them as left lane line or right lane line first. This can be achieved by checking the slope of each line segment. The slope of left lane line is negative, positive on the other side. After classify each line segments, I extend them to the same horizontal, compute their `x` coordinate with the same `y` coordinate. Finally, I average all left and right lane line's extended x coordinate to get an extrapolate left and right lane line respectively. The lane line which is more close to the car will have higher weight in average process.


![Step 5-2][step_5-2]

#### Step 6: Combine the lane line image with the original image
Finally, combining the lane line image found in previous step with the original input image as the final output.

![Step 6][step_6]


### 2. Potential shortcomings of current pipeline

* Hough line detection detects straight line, it will not work well as the road is curve.
* I suppose that the car is basically in the center of road, so I only need to check the center trapezoid part of image. But when the car is not in the center of the road, my pipeline will fail to find the lane line.
* When there is a car in front of my car. It's edge will also be detected as lane line and impact the correctness of the final result.


### 3. Improvement

* Use deep learning algorithm to detect lane line end-to-end may be more robust.
* Use some curve fitting technique may be more suitable for curve road.
* As images come from camera have time sequential property, I should consider the information of lane line found in previous image to let my pipeline output more stable result.
