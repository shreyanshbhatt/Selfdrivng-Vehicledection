## Writeup
---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps dont forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/test_1_result.jpg
[image2]: ./output_images/test_2_result.jpg
[image3]: ./output_images/test_3_result.jpg
[video1]: ./project_video_output_filterd.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README
### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

I started with training data provided with the project. Car and non-car images. The code for which and HOG features selection is in vehicle_detection.ipynb in codeblock IN[3].

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

#### 2. Explain how you settled on your final choice of HOG parameters.

I started with HSV color space and orientation of 9, pixel_per_cell = 8, cell_per_block = 2. I tried individual channels but the best results (test accuracy) I got was for all channels. Then, I tried using other color spaces. I did not notice a lot of difference between YUV, HSV, YCrCb. I ended up using YUV color space as combining that with color features resulted in good test accuracy.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained an SVM classifier with HOG features as described above. The training process is in .ipynb file in code block IN[4]. I started with using only HOG features with which I got about ~98.3% accuracy on the test set. I tried to improve the classifer using color features as well and used histogram features and spatial features with histbin=32 and spatial=(32, 32). All features were normalized and mean centered. The training data was randomized and then 20% of data was selected for testing.

It indeed improved test accuracy and the final accuracy I got was ~ 99.4%

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I implemented the sliding window in .ipynb in code block IN[6]. I used various scale value to scale the test image as well as video frame. Mostly used 75% overlapping as I wanted to detect car in as many window as possible and then to use high threshold value.

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Here are the output images of test images

![alt text][image1]
![alt text][image2]
![alt text][image3]

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here is a [link to my video result](./project_video_output_filterd.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

The code for detecting false positives and combining overlapping boxes is in .ipynb file code blocks 6, (mostly in ) 7, 19. I used the heatmap based approach as suggested in the project introduction. I started with sliding window based detection in the part of image (y=400, y=650). For some instances it detected car only for 1 window and hence the pixels, identified as car, appear only once in the heatmap. Hence, it is difficult to filter false positives. For this reason, I applied the sliding window approach for different configurations (varying scaling of images and different portions of images, code block IN[7]). So as to detect car pixels multiple times and filter out false positves (which do not get detected each time). I then used the 75% threshold (if a car appears ~ 75% of total configurations selected to detect car then only the pixel is identified as car pixel). The effect of which can be seen from example outputs in .ipynb file code block[19] (low heatmaps detected but rejected in final image) and the video project_video_output_withfp.mp4 (where I did not use different configurations to search car on).

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The pipeline can be smoother than it is by averaging the rectangle size over a few frames of video. Also, I am not sure if it works for the different weather and/or light conditions as the features are dependant on colors as well.
