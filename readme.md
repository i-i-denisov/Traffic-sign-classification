The first problem involves normalizing the features for your training and test data.

Implement Min-Max scaling in the `normalize()` function to a range of `a=0.1` and `b=0.9`. After scaling, the values of the pixels in the input data should range from 0.1 to 0.9.

Since the raw notMNIST image data is in [grayscale](https://en.wikipedia.org/wiki/Grayscale), the current values range from a min of 0 to a max of 255.

Min-Max Scaling:
$
X'=a+{\frac {\left(X-X_{\min }\right)\left(b-a\right)}{X_{\max }-X_{\min }}}
$

*If you're having trouble solving problem 1, you can view the solution [here](https://github.com/udacity/CarND-TensorFlow-Lab/blob/master/solutions.ipynb).*

# Traffic sign classifier
---
Goal of this project is to train a convolutional neural network to classify traffic sign images using german traffic sings [dataset](http://benchmark.ini.rub.de/?section=gtsrb&subsection=dataset).
Certain number of steps of this project can be distinguished:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report

[//]: # (Image References)

[image1]: ./writeup/random_sign_visualisation.png "Random sign"
[image2]: ./writeup/Dataset_label_count.png "Dataset sign distribution"
[image3]: ./writeup/Dataset_examples.png "Dataset overwiev"
[image4]: ./writeup/warp.png "Warp Example"
[image5]: ./writeup/warped_lines_drawn.jpg "Fit Visual"  


## Dataset exploration

This dataset consisnts of 51.839 32x32 RGB images distributed across 43 classes.
Each image contains only one traffic sign.
Examples of images for each sign in dataset:
![Alt_text][image3]

One image at closeup: 
![Alt text][image1]  

The dataset is split into three sets for training, validation and testing:
- Training Set:   34799 samples
- Validation Set: 4410 samples
- Test Set:       12630 samples

This is how classes are distributed across this sets:
![Alt_text][image2]
As you can see provided dataset is not balanced - we do not have uniform distribution of images across classes.

## Design and Test a Model Architecture
### Preprocessing
First step is to shuffle training dataset in order not to depend from image order in provided dataset.
Each RGB image is a set of integer numbers from 0 to 255 so it is pretty easy to normalize images to range -1..1 to minmize computational errors caused by operations with  values of different magnitude.
This is second pre-processing step. I tried to augment images with poisson noise for each of RGB layers - but it seems that it does not improve validation accuracy.
- Best validation accuracy with poisson noise augmentation: 0.947
- Best validation accuracy without augmentation: 0.941

### Model architechture

My final model consisted of the following layers:

| Layer         		|     Description	        					| 
|:---------------------:|:---------------------------------------------:| 
| Input         		| 32x32x3 RGB image   							| 
| Convolution 5x5x3     	| 1x1 stride, valid padding, outputs 28x28x12 	|
| RELU					|												|
| Max pooling 2x2	      	| 2x2 stride,  outputs 14x14x12 				|
| Convolution 5x5x3	    | 1x1 stride, valid padding, outputs 10x10x32      									|
|RELU                    |
|Max pooling 2x2|2x2 stride,  outputs 5x5x32|
|Flatten||
| Fully connected		| 800->120 								|
|RELU||
| Dropout				| 									|
|Fully connected		|120->84												|
|RELU||
|Dropout				|												|
|Fully connected| 84->43  

### Model training
Adam optimizer was used. For the final model following hyperparameters were used: 

|Hyperparameter|Value|
|:-----:|:------:|
|Learinng rate | 0.01|
| Number of epochs|30 |
|Batch size|2000|
|Dropout rate|0.5|

### Solution Approach
LeNet for MNIST was taken as a base model and without any modifications it showed around 89 percent accuracy, so I assumed that if we have 4 times more classes than in MNIST we should have more parameters. So I scaled LeNet model two times on all layers and continued to experiment with layers dimensions.
Comparison of different models I tested is provided in following table. Also, at start, I augmented dataset with RGB poisson noise, but it turned out it doesn't improve accuracy. 

| epochs | learning rate | batch size | dropout | RGB\Grayscale | augmentation      | conv1    | conv2     | fc1 output | fc2 output | validation accuracy | memory use |
|--------|---------------|------------|---------|---------------|-------------------|----------|-----------|------------|------------|---------------------|------------|
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x12x32 | 120        | 84         | 0.942               | 786303     |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x12x36 | 120        | 84         | 0.937               | 862740     |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x3x12 | 5x5x6x32  | 120        | 84         | 0.927               | 784597     |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x24 | 5x5x12x32 | 120        | 84         | 0.931               | 925005     |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x24 | 5x5x6x32  | 120        | 84         | 0.935               | 733180     |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x6x32  | 120        | 84         | 0.907               | 733180     |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x36 | 5x5x6x32  | 120        | 84         | 0.937               | 925005     |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x36 | 5x5x12x36 | 150        | 84         | 0.906               | 1049935    |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x36 | 5x5x6x36  | 150        | 84         | 0.936               | 1049851    |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x3x12 | 5x5x6x36  | 120        | 84         | 0.922               | 779916     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x3x12 | 5x5x6x36  | 120        | 84         | 0.935               | 860814     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x6x36  | 120        | 84         | 0.935               | 860804     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x3x12 | 5x5x12x36 | 120        | 84         | 0.918               | 862750     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x3x12 | 5x5x3x36  | 120        | 84         | 0.939               | 665456     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x3x36  | 120        | 84         | 0.912               | 553375     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x6x36  | 120        | 84         | 0.929               | 883296     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x12x32 | 120        | 84         | 0.931               | 862740     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x12x36 | 120        | 84         | 0.934               | 862740     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x12x36 | 120        | 84         | 0.934               | 862740     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x12x36 | 150        | 84         | 0.937               | 865265     |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x12x36 | 150        | 84         | 0.94                | 865265     |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x12x36 | 180        | 84         | 0.936               | 866195     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x12x36 | 180        | 84         | 0.936               | 866195     |
| 20     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x12x36 | 150        | 100        | 0.924               | 865564     |
30     | 0.01          | 2000       | 0.0     | RGB           | RGB poisson noise | 5x5x1x12 | 5x5x12x36 | 120        | 84         | 0.947               | 862740     |
| 30     | 0.01          | 2000       | 0.0     | RGB           | no                | 5x5x1x12 | 5x5x12x36 | 120        | 84         | 0.941               | 862740     |
| 30     | 0.01          | 2000       | 0.5    | RGB           | no                | 5x5x1x12 | 5x5x12x36 | 120        | 84         | 0.951               | 862740     |
| 100     | 0.001          | 2000       | 0.5    | RGB           | no                | 5x5x1x12 | 5x5x12x36 | 120        | 84         | 0.942              

Training takes about 4 minutes on GT740M GPU with 1.5 Gb RAM.
