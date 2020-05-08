# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report



The goal of this project is to create a pipeline for detecting for detecting lane lines. It involes analyzing the lane lines firstly in an image, developing a pipeline as described later below and extending the same pipeline to a video file. The original image referenceon which the pipeline was developed is shown below:

[test_images/image.jpg]

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. 

1. GrayScale Conversion: I converted the images to grayscale, which helped me analyze the image better in terms of only pixel strength.

[test_images_output/gray.jpg]

2. Gaussian Blur: Then I applied a Gaussian Blur to reduce the noise in the image and reduce some details that are unnecessary. I used a kernel size of 5.
3. Canny Edge Detection: Then I passed the image through a canny edge detection to accurately detect the edges in the image. Since lane lines are mostly edges, this helps us narrow down the possible lane location in the image. I chose thefollowing values for my Canny Edge Detection:
low_threshold = 50
high_threshold = 120

[test_images_output/edges.jpg]

4. Defining Range of Interest using a Polynomial: Now I defined a range of interest by defining a polynomial to cover the location in the image where I expect my lane lines to appear and mask the rest of the image. I chose the following vertices:
(40,550),(900, 550), (500, 295), (510,295)
5. Hough Transform: Then I performed a hough transform on the edge detected masked image which would give me the lanes detected within the polynomia mask. I used the following rho ,theta, threshold, min line length and max line gap values:
rho = 3
theta = 1*np.pi/180
threshold = 120
min_line_length = 70
max_line_gap =110

[test_images_output/color_edges.jpg]

After this I created a weighted image with the lined image on the original image to obtain a reference.

[test_images_output/combo.jpg]

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by:

Firstly I considered the x1, x2, y1, y2 values of each line which was being detected. I calculated the slopes and the intercepts of each of these lines. Then I seperated these lines into possible left lane detection and right lane detection using the slope i.e., if slope is negative then it is possiby a left lane and if the slope is a positive value, then it is a possible right lane detection.

Now the lines are seperated into left slopes and intercepts and right slopes and intercepts. Now I calculate the average slope and intercept for both the left and right side to obtain one slope, intercept each for left and right side. This is the single line that I would like to draw.

Then I found the x and y intercepts of both sides of the lane by the following method:
Left Lane: I considered the y intercept to be the maximum (size of the image) since it is plotted at the bottom part of the image, and I used the equation of the line to calculate x intercept.
For the second point, I finalized on 0.6 times of the max yvalue after a few iterations and then calculated the relevant x intercept

Right Lane: Since the right lane is towards the bottom right of the image, I considered the first x intercept as the maximum value (size of the image) and calculated the relevant y intercept using equation of the line
For the second point, I considered 0.5 times of the max x intercept and calculated the appropriate y intercept.

Now I have two points for the left lane and two pints for the right lane.

When I executed this function on the video, I got an error because one of the frames found no left lanes. So I created global variables to hold the previous value for such cases.


If you'd like to include images to show how the pipeline works, here is how to include an image: 

[test_images_output/combo.jpg]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when we are trying to detect curved lane lines. Since we always draw a straigth line, the better solution would be to draw a curve and modify the weights to draw a straight line if needed.

Another shortcoming is definetely using grayscale initially instead of HSV. I did see a lot of people using HSV to process the image better and diffrentiating the yellow and white lines. I think that is a better approach when you have different objects in the video such as the bridgein the optional challenge video.

Additionally I think my code could have been more short or compact by using more functions but I wanted to keep it simple due to my relatively less experience with python.


### 3. Suggest possible improvements to your pipeline

Possible Improvements as I mentioned earlier would be to definetely plot a curve instead of a straight line. Ialso think it would be helpful if we save the previous lane information and perform a sort of comparison with thecurrent line data to avoid errors and catch mis detection sooner.

There can be more accuracy achieved in the initial image detection using the HSV approach. It would definetely help eliminate wrong detection incase of shadows, or cloudy/rainy/snowy days which the gray scale conversion possibly would not catch.

Reflecting on the overall project, I particularly had difficuly only in the actual syntax portion of the code which I aim to compensate by going through some python training. The actual logic is quite simple and hnestlyquite interesting to work on.

