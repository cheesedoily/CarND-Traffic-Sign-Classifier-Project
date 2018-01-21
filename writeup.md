**Finding Lane Lines on the Road**


[image1]: ./images/histogram.png "histogram"
[image2]: ./images/original.png "original"
[image3]: ./images/grayscale.png "grayscale"
[image4]: ./images/equalized.png "equalized"
[image5]: ./extra-images/40-roundabout_mandatory.jpg "roundabout"
[image5]: ./extra-images/17-no_entry.jpg "no entry"


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

1. Convolution Layer 5x5 with depth 6 and 2x2 max pooling to subsample
2. Convolution Layer 5x5 with depth 16 and 2x2 max pooling to subsample
3. Fully connectied hidden layer with of size 120, with 80% dropout
4. Fully connected hidden layer with of size 84, with 80% dropout
5. Fully connected output layer with size 43, corresponding to the number of labels


### Model Training

I tinkered with some of the hyperparameters around model training, but didn't find a material impact. I ended up reverting back to the LeNet model for all hyperparamters. Overall, the model is overfitting since it performs well on training/validation but not as well on the test. The largest improvement I made as far as model training was to augment the test data set by rotating each image a a little in each direction, creating 3x larger training set (origin, counter-clockwise, clockwise). I imagine that it would be possible to implement various distortions to any given image to similulate how a true image may be detected by a camera (perspective, blurs, etc).

EPOCHS = 10
BATCH_SIZE = 128
Test Accuracy = 0.941

### 3. Test a Model on New Images

The model only performed 60% on images from the web vs the 93+% on the test set. Looking at the softmax, I noticed that the correct label was usually the second most likely choice if not the first, so that's good. The dataset provided in some ways has been normalized by the curator of the data set to ensure images are roughly inline with one another. That human bias wasn't present as I found images on the web. I think this basically means in some ways that there is some leaking between test and training. When I through innocuous images at the model without this bias, performance is fairly weak. I think that the below approaches would go a long way in making the model more robust.

```
Correct label 17
label 17 with probability 99.99977350234985%
label 38 with probability 0.0002296828142789309%
label 12 with probability 4.0134628953580886e-08%
label 41 with probability 5.949251074416351e-10%
label 14 with probability 2.3589181823158167e-10%

Correct label 29
label 1 with probability 61.59331202507019%
label 2 with probability 32.88537859916687%
label 19 with probability 2.923642471432686%
label 9 with probability 1.5563751570880413%
label 6 with probability 1.041300967335701%

Correct label 33
label 33 with probability 98.39504957199097%
label 38 with probability 0.975803192704916%
label 37 with probability 0.31284091528505087%
label 1 with probability 0.22186047863215208%
label 17 with probability 0.09444410097785294%

Correct label 40
label 12 with probability 100.0%
label 2 with probability 2.884516514534141e-08%
label 40 with probability 1.3232772516635904e-09%
label 5 with probability 3.3933576434885815e-11%
label 38 with probability 1.7408471853722768e-11%

Correct label 9
label 9 with probability 99.52823519706726%
label 10 with probability 0.3651408478617668%
label 16 with probability 0.08274962310679257%
label 3 with probability 0.012839463306590915%
label 32 with probability 0.011046891449950635%
```

The first image a basically as clear as it gets so the model doesn't have much noise to confuse it.

![alt test][image6]


Above we notice that the 4th image is incorrectly predicted with nearly 100% confidence, mistaking it for Priority road (12) which is more of a diamond shape as opposed to circle. I imagine that blue background relative to the sign post itself which is also blue may be causing issues. 

![alt text][image5]


### Suggest possible improvements to your pipeline

The biggest thing I would do to improve the accuracy of the model would be to add many more distortions to the training as mentioned before. Another strategy would be to to more equally rebalance the classes found in the training set so that the model doesn't overfit for any specific class. I would require a minimum number of training examples per class and distort images randomly from the original set before adding to this new, larger test set. Lesson learned: more test data the better!
