---
layout: post
title: Keras CNN for Binary Classification of Hand-Drawn Images
---
Patrick Stock 2018
Check out full [Jupyter notebook here](https://github.com/pstock175/ml-portfolio/blob/master/CNN.ipynb)

### *Data Source*
The sample sketches come from the Google Quick, Draw! dataset:https://quickdraw.withgoogle.com/data There are many objects represented here, but only Apples and Bananas will be considered here. A few other things to know for this program:

The Apple and Banana data sets have ~150,000 sketches each. To keep the analysis simple, only 1,500 will be used from each set. The CNN can still produce accurate results even with the reduced size.
The Quick, Draw! dataset offers multiple download options, including time-series representations that contain information about the order of drawing of the sketch. This network will only consider the simplified "final result" sketch
First, the .npy Numpy bitmap files for Apple and Banana are downloaded from the following link, and saved to the working directory: https://console.cloud.google.com/storage/browser/quickdraw_dataset/full/numpy_bitmap
```python
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
from keras.models import Sequential
from keras.layers import Conv2D, MaxPooling2D, Flatten, Dense

apples = np.load('full_numpy_bitmap_apple.npy')
bananas = np.load('full_numpy_bitmap_banana.npy')
```
### *Data Processing*
In total, 3000 labelled examples will be used: 1500 apples and 1500 bananas. The image_dataset() list will be a 3000 element list of tuples where the first element of the tuple is the 28x28 Numpy array representing the sketch, and the second element of the tuple is the label. Apple will be indicated by 1, and Banana indicated by 0.
```python
num_images = 1500
image_dataset = []

# Reshape arrays to 28x28 and create tuple with label
# Images must have a 3rd dummy dimension (28x28x1) for Conv2D layer
for image in range(num_images):
    reshaped_apple = np.reshape(apples[image],(28,28))
    reshaped_apple = reshaped_apple[:,:,np.newaxis]
    image_dataset.append([reshaped_apple,1])
    
    reshaped_banana = np.reshape(bananas[image],(28,28))
    reshaped_banana = reshaped_banana[:,:,np.newaxis]
    image_dataset.append([reshaped_banana,0])

from random import shuffle
shuffle(image_dataset)

#Display Samples from the Dataset
sample_apple = np.reshape(apples[1],(28,28))
sample_banana = np.reshape(bananas[2],(28,28))

plt.imshow(sample_apple, cmap='Greys')
plt.show()
plt.imshow(sample_banana, cmap='Greys')
plt.show()
```
![](https://imgur.com/a/U9T9Q9U)

### *Keras CNN Architecture*
### 2D-Convolution
The architecture is a 2-layer model with 2D-Convolutions and Max-Pooling Layers. The image input space is 28x28x1, and each convolution layer uses 14 convolution filters, each 3x3x1. The selection of 14 filters is somewhat arbitrary and can be adjusted to tune model performance and accuracy. The input images are simple and do not have elements that more complex images have such as shading or color. Because of this, tending towards a lower number of convolution filters per layer will still likely produce good results. Additionally, since no stride is specified the default value of 1 will be used, meaning that the output volume will be an activation map of dimension 28x28x8.

### Max Pooling Layers
The Max Pooling Layers exist to reduce the image size while still preserving the locations of features relative to one another. The pool size is set to 2, reducing the activation map volume to 14x14x8. Since the input space is so small for these images at only 28x28 pixels, it is wise to keep the pool size small to avoid losing too much resolution with the images.

### Flattening & Fully-Connected Layers
Flattening layers simply unroll the multi-dimensional array to a single vector in preparation for the fully-connected final layers. The final fully-connected layer uses a sigmoid activation to make a probability estimate between liklihood of the image being an apple (1) vs a banana (0)
```python
model = Sequential()
model.add(Conv2D(14,(3,3), input_shape = (28,28,1), activation = 'relu'))
model.add(MaxPooling2D(pool_size=(2,2)))

model.add(Conv2D(14,(3,3), activation='relu'))
model.add(MaxPooling2D(pool_size=(2,2)))

model.add(Flatten())

model.add(Dense(64,activation= 'relu'))
model.add(Dense(1,activation= 'sigmoid'))

model.compile(optimizer='adam', loss = 'binary_crossentropy', metrics = ['accuracy'])
```
### *Training*
2500 samples are designated for training with the remaining 500 for the test set. The default batch size is used for 25 training epochs.
```python
X_train = np.array([entry[0] for entry in image_dataset[:2500]])
y_train = np.array([entry[1] for entry in image_dataset[:2500]])
X_test = np.array([entry[0] for entry in image_dataset[2500:]])
y_test = np.array([entry[1] for entry in image_dataset[2500:]])

model.fit(X_train, y_train, epochs = 25)
```
### *Testing*
The accuracy on the training set reached 99.08%. The remaining 500 samples can be used to generate predictions and evaluate how well the model generalizes. The binary accuracy metric is used to evaluate performance.
```python
y_pred = model.predict(X_test, verbose=1)

fromfrom  sklearn.metricssklearn.  import accuracy_score
accuracy = accuracy_score(y_test,y_pred.astype(int))
print(accuracy)
```
#### Classification accuracy of 91.2% is acheieved on the test set with relatively little tuning to the model

