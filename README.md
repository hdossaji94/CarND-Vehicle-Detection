---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.


## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][ReadmeImages/VehicleandNonVehicle.JPG]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][ReadmeImages/HOGImages.JPG]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters to troubleshoot and the ones below gave me the best results so far. 

![alt text][ReadmeImages/Parameters.JPG]


#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using the above given parameters. I timed the seconds it took to train the classifier as well. It took about 13.98 seconds to train the classifier. Below I have given an image of the fitModel function which trains the classifier given the parameters above. 

![alt text][ReadmeImages/FitModelFunction.JPG]


### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

After testing multiple different scales and different overlapping windows. I eventually came to an okay solution that would work for further development. Below are given some of the prelimary images I was able to process using sliding windows search. 

![alt text][ReadmeImages/SlidingWindows.JPG]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result. To offset the multiple flase detections, I used heatmaps, thresholding and labeling to determine the cars. Here are some example images:

![alt text][ReadmeImages/HeatmapResult.JPG]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_output_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here is a frame and its corresponding heatmap:

![alt text][ReadmeImages/HeatMap.JPG]



### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further. 

The video does seem to have some problems differentiating between what is a car and what is not a car at a couple of frames so the classifier could be trained a little bit better. 

The output video also does pick up on some boxes on the opposite side of the road. Now in some cases, it could be picking up the opposite traffic, but in other cases it was random noise. So maybe the thresholding for the heatmaps could be set higher. However when I did set the threshold higher, the cars on the right side of the road seemed to lose its label as it drove further away from the vehicle. 

The pipeline also has trouble dealing with shadows from the trees in the test images, so maybe augmenting the images further to train the classifier could have helped. 

