**Finding Lane Lines on the Road**


[image1]: ./images/histogram.png "histogram"
[image2]: ./images/original.png "original"
[image3]: ./images/grayscale.png "grayscale"
[image4]: ./images/equalized.png "equalized"


---

### 1. Dataset Exploration

### Dataset Summary

Number of training examples = 34799
Number of testing examples = 12630
Image data shape = (32, 32, 3)
Number of classes = 43

### Exploratory Visualization

In the notebook HTML file you can see that I plotted a random image and the histogram across the train, validation and test sets. It very clear that the various classes are represented differently in each of these sets which could result in over fitting (histogram of the relative occurence below)

![alt text][image1]


### 2. Design and Test a Model Architecture

### Preprocessing

I used a very basic preprossesing step of first grayscaling the image (using a weighted approach), the doing histogram equalization in order to improve contrast and normalizing the image to obtain pixel values between (-1, 1). I admittedly was a little suprised that the color channels were not helpul to the model overall and that squashing down to grayscale produced better results.

Original: ![alt text][image2]

Grayscale: ![alt text][image3]

Equalized: ![alt text][image4]


### Model Architecture

I used the LeNet architecture, with only one addition which was to include drop out layer with keep_probability of 80% after each fully connected hidden layer. Otherwise the structure remained the same after accounting for a 32x32 image vs 28x28 which really only affected the first convolutional layer.

### Model Training

I tinkered with some of the hyperparameters around model training, but didn't find a material impact. I ended up reverting back to the LeNet model for all hyperparamters. Overall, the model is overfitting since it performs well on training/validation but not as well on the test. The largest improvement I made as far as model training was to augment the test data set by rotating each image a a little in each direction, creating 3x larger training set (origin, counter-clockwise, clockwise). I imagine that it would be possible to implement various distortions to any given image to similulate how a true image may be detected by a camera (perspective, blurs, etc).

EPOCHS = 10
BATCH_SIZE = 128

### 3. Test a Model on New Images

The model only performed 60% on images from the web vs the 93+% on the test set. Looking at the softmax, I noticed that the correct label was usually the second most likely choice if not the first, so that's good. The dataset provided in some ways has been normalized by the curator of the data set to ensure images are roughly inline with one another. That human bias wasn't present as I found images on the web. I think this basically means in some ways that there is some leaking between test and training. When I through innocuous images at the model without this bias, performance is fairly weak. I think that the below approaches would go a long way in making the model more robust.

### Suggest possible improvements to your pipeline

The biggest thing I would do to improve the accuracy of the model would be to add many more distortions to the training as mentioned before. Another strategy would be to to more equally rebalance the classes found in the training set so that the model doesn't overfit for any specific class. I would require a minimum number of training examples per class and distort images randomly from the original set before adding to this new, larger test set. Lesson learned: more test data the better!
