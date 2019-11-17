# **Finding Lane Lines on the Road** 
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />

Overview
---

<!-- When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.-->

<!-- In this project you will detect lane lines in images using Python and OpenCV.  OpenCV means "Open-Source Computer Vision", which is a package that has many useful tools for analyzing images.  

<!-- To complete the project, two files will be submitted: a file containing project code and a file containing a brief write up explaining your solution. We have included template files to be used both for the [code](https://github.com/udacity/CarND-LaneLines-P1/blob/master/P1.ipynb) and the [writeup](https://github.com/udacity/CarND-LaneLines-P1/blob/master/writeup_template.md).The code file is called P1.ipynb and the writeup template is writeup_template.md  -->

<!-- To meet specifications in the project, take a look at the requirements in the [project rubric](https://review.udacity.com/#!/rubrics/322/view) -->

**Finding Lane Lines on the Road**

The goals of this project are the following:
* Make a pipeline that finds lane lines on the road in image and video files
* Reflect on this work in this written report ([/README.md](/README.md))

The project pipeline uses the following directories containing image and video files:
- [/test_images/](/test_images/): Directory containing test image files
- [/test_images_output/](/test_images_output/): Directory containing output of processed test image files
- [/test_videos/](/test_videos/): Directory containing test video files
- [/test_videos_output/](/test_videos_output/): Directory containing output of processed test video files

The project is run in a Jupyter notebook:
- [/P1.ipynb](/P1.ipynb): Jupyter notebook containing Python code to run the image processing pipeline

<!-- [/examples/]: # (Example Images) -->
<!-- [image1]: ./examples/grayscale.jpg "Grayscale" -->


<!-- Creating a Great Writeup -->
<!-- --- -->
<!-- For this project, a great writeup should provide a detailed response to the "Reflection" section of the [project rubric](https://review.udacity.com/#!/rubrics/322/view). There are three parts to the reflection: -->

<!-- 1. Describe the pipeline -->

<!-- 2. Identify any shortcomings -->

<!-- 3. Suggest possible improvements -->

<!-- We encourage using images in your writeup to demonstrate how your pipeline works.   -->

<!-- All that said, please be concise!  We're not looking for you to write a book here: just a brief description. -->

<!-- You're not required to use markdown for your writeup.  If you use another method please just submit a pdf of your writeup. Here is a link to a [writeup template file](https://github.com/udacity/CarND-LaneLines-P1/blob/master/writeup_template.md).  -->


The Project Setup and General Instructions
---

<!-- ## If you have already installed the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) you should be good to go!   If not, you should install the starter kit to get started on this project. ## -->

**Step 1:** Set up the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) <!-- if you haven't already. -->

**Step 2:** Open the code in a Jupyter Notebook

<!-- You will -->Complete the project code in a Jupyter notebook [/P1.ipynb](/P1.ipynb).  <!--  If you are unfamiliar with Jupyter Notebooks, check out [Udacity's free course on Anaconda and Jupyter Notebooks](https://classroom.udacity.com/courses/ud1111) to get started. -->

<!-- Jupyter is an Ipython notebook where you can run blocks of code and see results interactively.  All the code for this project is contained in a Jupyter notebook. To start Jupyter in your browser, use terminal to navigate to your project directory and then run the following command at the terminal prompt (be sure you've activated your Python 3 carnd-term1 environment as described in the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) installation instructions!): -->

<!-- `> jupyter notebook` -->

<!-- A browser window will appear showing the contents of the current directory.  Click on the file called "P1.ipynb".  Another browser window will appear displaying the notebook.  Follow the instructions in the notebook to complete the project. -->

**Step 3:** Complete the project and submit both the Ipython notebook and the project writeup

---

### Reflection

### 1. Pipeline Description

The project pipeline consists of 6 steps:

1. Convert images to grayscale
2. Apply a Gaussian blur to the grayscale images to subdue noise
3. Apply a Canny filter to the blurred images to find edges
4. Mask the filtered image using a region of interest (roi)
5. Apply a Hough transformation (i.e., analysis in parameter space) for to extract lane line features
6. Draw lines on the image that overlay the left and right lanes on the road

In order to draw a single line on the left and right lanes in the image, the given function `draw_lines()` was modified:

1. Left lane lines were determined to have a negative slope
    - This determination was based on image coordinates, where the top-left coordinates of the image is (x = 0, y = 0), and the bottom-right coordinates of the image is (x = image width, y = image height)  
    - Determining slope using Cartesian coordinates would result in left and right lane lines being swapped since the y-value increases in a downward direction in image data
2. Left lane lines were also determined to have a maximum x-value of the top-right roi vertex
    - This maximum x-value was chosen to prevent left lane lines from being detected on the right side of the roi
3. Right lane lines were determined to have a positive slope
    - This determination was based on image coordinates, where the top-left coordinates of the image is (x = 0, y = 0), and the bottom-right coordinates of the image is (x = image width, y = image height)  
    - Determining slope using Cartesian coordinates would result in left and right lane lines being swapped since the y-value increases in a downward direction in image data
4. Right lane lines were also determined to have a minimum x-value of the top-left roi vertex
    - This minimum x-value was chosen to prevent right lane lines from being detected on the left side of the roi
5. The detected left lane overlay was drawn in green, and the detected right lane overlay was drawn in red

<!-- If you'd like to include images to show how the pipeline works, here is how to include an image: -->
The Jupyter notebook, [/P1.ipynb](P1.ipynb), demonstrates the process of the pipeline operated on the still images with the following output:

1. The original image
2. The image after the grayscale conversion, Gaussian blur, Canny filter, and roi are applied
3. The image after the Hough transformation is applied, including lane line overlays
    - The Hough transformation also calls the `draw_lines()` function, resulting in the lane line overlays
4. The combined image of the original image and the processed image after the Hough transformation is applied
    - This is performed using the `weighted_img()` function

<!-- ![alt text][image1] -->


### 2. Potential Shortcomings with the Current Pipeline

**Region of Interest**
The roi is statically assigned.  This could possibly lead to undesired results when driving with steep grades (uphill/downhill) or taking sharp corners.

**Code Sophistication**
The `draw_lines()` function sums the totals of the `x1`, `y1`, `x2`, and `y2`, values in a loop.  After the loop is complete, the summed values are divided by the counted number of lines to acquire an average (i.e., statistical mean).  

**Optional Challenge**
Shortcomings of the project code can be seen in the Optional Challenge.  The images in the Optional Challenge video show a very different field of view, resulting in the `draw_lines()` function outputting lines that do not match the lanes on the road. 

<!--One potential shortcoming would be what would happen when ...  -->

<!--Another shortcoming could be ... -->


### 3. Possible Improvements to the Pipeline

Possible improvements to the pipeline are stated in regard to its observed shortcomings.

**Region of Interest**
Finding a way to dynamically assign a region of interest based on an Hough transformation "pre-processing" could improve lane lane detection under a greater variety of driving scenarios.

**Code Sophistication**
While the method used in the current `draw_lines()` function code works sufficiently for the provided examples (with the exception of the Optional Challenge), processing cost could possibly be improved with an average calculation and zero-slope data elimination by the use of `numpy.nanmean`.  

**Optional Challenge**
Drastic changes would likely be in order in regard to the image processing parameters (Gaussian blur, Canny edge detection, roi masking, and Hough transformation) to find the proper lane lines in both the test images/videos, as well as the Optional Challenge video.

<!-- A possible improvement would be to ... -->


<!-- ## How to write a README -->
<!-- A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777). -->

HTML("""
<video width="960" height="540" controls>
  <source src="test_videos_output/solidWhiteRight.mp4">
</video>
""".format(white_output))
