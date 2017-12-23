---
layout:     post
title:      Scene Classification with Deep Learning
date:       2017-12-22 14:12:22
summary:    Scene Classification with Deep Learning
categories: computer-vision machine-learning deep-learning
---
<img src="{{ base }}/images/cv/proj6/header.png" />

> This project report was compiled as part of the course requirements of [CS 6476 Computer Vision](https://www.cc.gatech.edu/~hays/compvision/) under [Prof. James Hays](https://www.cc.gatech.edu/~hays/)

In this project we use convolutional neural networks to classify scenes into categories. Convolutional refers to the convolutional nature of the intermediate layers. The activations of the cells in a conv layer are a result of a convolution of multiple cells in the previous layer, much like the convolution of matrices. When a convolutional layer derives its activations from all channels of the previous layer it is termed as a fully convolutional layer.

In Part 1, we train a neural network from scratch and in Part 2, we use the pretrained VGG network and fine tune it to our data.

## Part 1

To start off, we build a CNN ground up and train it.

### Data Jittering 

As we do not have enough data, we perform data jittering to augment our data set. We perform data jittering by:

* Image flipping: Flip the image horizontally
* Random Image rotation: Rotate the image slightly
* Random Image scaling: Scale the image slightly

### Zero centering 
To normalize the data set, we subtract the average of the data set from each of the image.

### Network Regularization 
Regularization is used to fight overfitting of the model to the dataset. We do this by using a dropout layer, which randomly switches connections on and off between layers. This prevents a unit in one layer from relying too strongly on a single unit in the previous layer.

### Network Architecture
We train a network to categorize images in the 15 scene categories. The following is the architecture of the network

| -------    | -----        |------                                      | ------       | -------           				    | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 
| layer      |0             |1                                           |2             |3                  				    |4      	| 5                                             |6       |7                                      | 8      | 9           | 10                                | 11       |
| -------    | -----        |------                                      | ------       | -------           				    | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 
| Type       |Input         |Conv                                        | BNorm        | MaxPool           				    | ReLU   	| Conv                                          | BNorm  | MaxPool                               | ReLU   | Dropout     | FullConv                          | Soft Max |
| Details    |Input Image   |Filter Size: 9<br> Stride 1<br> Filters: 10 |              | Pool Size: 6<br> Pad: 5<br> Stride: 6 |       	| Filter Size: 38<br> Stride 6<br> Filters: 15  |        | Pool Size: 6<br> Pad: 5<br> Stride: 6 |        | Rate: 0.5   | Filter Size: 3<br> Filters: 15    |          |
| Dimensions |64x64x3       | 56x56x10                                   | 56x56x10     | 11x11x10                              | 11x11x10  | 7x7x15                                        | 7x7x15 | 3x3x15                                | 3x3x15 | 3x3x15      | 1x1x15                            | 1x1x15   |
| -------    | -----        |------                         			 | ------       | -------                               | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 

**Result**

<img src="{{ base }}/images/cv/proj6/part1/network 18/trainingCurves.png" width="50%" style="float:left"/>
<img src="{{ base }}/images/cv/proj6/part1/network 18/FirstLayerFilters.png" width="50%" style="float:left"/>

The given network, produces an accuracy of 57.8% with a learning rate of 0.01 in 50 epochs


---

### Architecture Experimentation

The following networks are modified versions of the network specified in the project description. A constant learning rate of 0.001 was used for each of the following networks. The difference in the networks can be realised by examining the 10th layer (Full Conv1)

#### Full Conv Layer Resolution: 8

| layer      |0             |1                                           |2             |3                  				    |4      	| 5                                             |6       |7                                      | 8      | 9           | 10                                | 11       |
| -------    | -----        |------                                      | ------       | -------           				    | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 
| Type       |Input         |Conv                                        | BNorm        | MaxPool           				    | ReLU   	| Conv                                          | BNorm  | MaxPool                               | ReLU   | Dropout     | FullConv                          | Soft Max |
| Details    |Input Image   |Filter Size: 9<br> Stride 1<br> Filters: 10 |              | Pool Size: 6<br> Pad: 5<br> Stride: 6 |       	| Filter Size: 38<br> Stride 6<br> Filters: 8   |        | Pool Size: 6<br> Pad: 5<br> Stride: 6 |        | Rate: 0.5   | Filter Size: 3<br> Filters: 15    |          |
| Dimensions |64x64x3       | 56x56x10                                   | 56x56x10     | 11x11x10                              | 11x11x10  | 7x7x8                                         | 7x7x8  | 3x3x8                                 | 3x3x8  | 3x3x8       | 1x1x15                            | 1x1x15   |
| -------    | -----        |------                         			 | ------       | -------                               | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 


Results

<img src="{{ base }}/images/cv/proj6/part1/network 8/trainingCurves.png" width="50%" style="float:left"/>
<img src="{{ base }}/images/cv/proj6/part1/network 8/FirstLayerFilters.png" width="50%" style="float:left"/>

Lowest validation error for above network: 0.494000

---

#### Full Conv Layer Resolution: 10

| layer      |0             |1                                           |2             |3                  				    |4      	| 5                                             |6       |7                                      | 8      | 9           | 10                                | 11       |
| -------    | -----        |------                                      | ------       | -------           				    | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 
| Type       |Input         |Conv                                        | BNorm        | MaxPool           				    | ReLU   	| Conv                                          | BNorm  | MaxPool                               | ReLU   | Dropout     | FullConv                          | Soft Max |
| Details    |Input Image   |Filter Size: 9<br> Stride 1<br> Filters: 10 |              | Pool Size: 6<br> Pad: 5<br> Stride: 6 |       	| Filter Size: 38<br> Stride 6<br> Filters: 10  |        | Pool Size: 6<br> Pad: 5<br> Stride: 6 |        | Rate: 0.5   | Filter Size: 3<br> Filters: 15    |          |
| Dimensions |64x64x3       | 56x56x10                                   | 56x56x10     | 11x11x10                              | 11x11x10  | 7x7x10                                        | 7x7x10 | 3x3x10                                | 3x3x10 | 3x3x10      | 1x1x15                            | 1x1x15   |
| -------    | -----        |------                         			 | ------       | -------                               | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 


Results

<img src="{{ base }}/images/cv/proj6/part1/network 10/trainingCurves.png" width="50%" style="float:left"/>
<img src="{{ base }}/images/cv/proj6/part1/network 10/FirstLayerFilters.png" width="50%" style="float:left"/>


Lowest validation error for above network: 0.457333

---

#### Full Conv Layer Resolution: 12

| layer      |0             |1                                           |2             |3                  				    |4      	| 5                                             |6       |7                                      | 8      | 9           | 10                                | 11       |
| -------    | -----        |------                                      | ------       | -------           				    | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 
| Type       |Input         |Conv                                        | BNorm        | MaxPool           				    | ReLU   	| Conv                                          | BNorm  | MaxPool                               | ReLU   | Dropout     | FullConv                          | Soft Max |
| Details    |Input Image   |Filter Size: 9<br> Stride 1<br> Filters: 10 |              | Pool Size: 6<br> Pad: 5<br> Stride: 6 |       	| Filter Size: 38<br> Stride 6<br> Filters: 12  |        | Pool Size: 6<br> Pad: 5<br> Stride: 6 |        | Rate: 0.5   | Filter Size: 3<br> Filters: 15    |          |
| Dimensions |64x64x3       | 56x56x10                                   | 56x56x10     | 11x11x10                              | 11x11x10  | 7x7x12                                        | 7x7x12 | 3x3x12                                | 3x3x12 | 3x3x12      | 1x1x15                            | 1x1x15   |
| -------    | -----        |------                         			 | ------       | -------                               | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 

Results

<img src="{{ base }}/images/cv/proj6/part1/network 12/trainingCurves.png" width="50%" style="float:left"/>
<img src="{{ base }}/images/cv/proj6/part1/network 12/FirstLayerFilters.png" width="50%" style="float:left"/>

Lowest validation error for above network: 0.436000

---

#### Full Conv Layer Resolution: 18

| layer      |0             |1                                           |2             |3                  				    |4      	| 5                                             |6       |7                                      | 8      | 9           | 10                                | 11       |
| -------    | -----        |------                                      | ------       | -------           				    | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 
| Type       |Input         |Conv                                        | BNorm        | MaxPool           				    | ReLU   	| Conv                                          | BNorm  | MaxPool                               | ReLU   | Dropout     | FullConv                          | Soft Max |
| Details    |Input Image   |Filter Size: 9<br> Stride 1<br> Filters: 10 |              | Pool Size: 6<br> Pad: 5<br> Stride: 6 |       	| Filter Size: 38<br> Stride 6<br> Filters: 18  |        | Pool Size: 6<br> Pad: 5<br> Stride: 6 |        | Rate: 0.5   | Filter Size: 3<br> Filters: 15    |          |
| Dimensions |64x64x3       | 56x56x10                                   | 56x56x10     | 11x11x10                              | 11x11x10  | 7x7x18                                        | 7x7x18 | 3x3x18                                | 3x3x18 | 3x3x18      | 1x1x15                            | 1x1x15   |
| -------    | -----        |------                         			 | ------       | -------                               | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 

Results

<img src="{{ base }}/images/cv/proj6/part1/network 18/trainingCurves.png" width="50%" style="float:left"/>
<img src="{{ base }}/images/cv/proj6/part1/network 18/FirstLayerFilters.png" width="50%" style="float:left"/>

Lowest validation error for above network: 0.412667

---

#### Full Conv Layer Resolution: 19

| layer      |0             |1                                           |2             |3                  				    |4      	| 5                                             |6       |7                                      | 8      | 9           | 10                                | 11       |
| -------    | -----        |------                                      | ------       | -------           				    | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 
| Type       |Input         |Conv                                        | BNorm        | MaxPool           				    | ReLU   	| Conv                                          | BNorm  | MaxPool                               | ReLU   | Dropout     | FullConv                          | Soft Max |
| Details    |Input Image   |Filter Size: 9<br> Stride 1<br> Filters: 10 |              | Pool Size: 6<br> Pad: 5<br> Stride: 6 |       	| Filter Size: 38<br> Stride 6<br> Filters: 19  |        | Pool Size: 6<br> Pad: 5<br> Stride: 6 |        | Rate: 0.5   | Filter Size: 3<br> Filters: 15    |          |
| Dimensions |64x64x3       | 56x56x10                                   | 56x56x10     | 11x11x10                              | 11x11x10  | 7x7x19                                        | 7x7x19 | 3x3x19                                | 3x3x19 | 3x3x19      | 1x1x15                            | 1x1x15   |
| -------    | -----        |------                         			 | ------       | -------                               | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 


Results

<img src="{{ base }}/images/cv/proj6/part1/network 19/trainingCurves.png" width="50%" style="float:left"/>
<img src="{{ base }}/images/cv/proj6/part1/network 19/FirstLayerFilters.png" width="50%" style="float:left"/>

Lowest validation error for above network: 0.416667

---
   
#### Full Conv Layer Resolution: 20

| layer      |0             |1                                           |2             |3                  				    |4      	| 5                                             |6       |7                                      | 8      | 9           | 10                                | 11       |
| -------    | -----        |------                                      | ------       | -------           				    | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 
| Type       |Input         |Conv                                        | BNorm        | MaxPool           				    | ReLU   	| Conv                                          | BNorm  | MaxPool                               | ReLU   | Dropout     | FullConv                          | Soft Max |
| Details    |Input Image   |Filter Size: 9<br> Stride 1<br> Filters: 10 |              | Pool Size: 6<br> Pad: 5<br> Stride: 6 |       	| Filter Size: 38<br> Stride 6<br> Filters: 20  |        | Pool Size: 6<br> Pad: 5<br> Stride: 6 |        | Rate: 0.5   | Filter Size: 3<br> Filters: 15    |          |
| Dimensions |64x64x3       | 56x56x10                                   | 56x56x10     | 11x11x10                              | 11x11x10  | 7x7x20                                        | 7x7x20 | 3x3x20                                | 3x3x20 | 3x3x20      | 1x1x15                            | 1x1x15   |
| -------    | -----        |------                         			 | ------       | -------                               | ----- 	|------                                         | ------ | -------                               | ------ | -------     | --------                          | -------- | 

Results

<img src="{{ base }}/images/cv/proj6/part1/network 20/trainingCurves.png" width="50%" style="float:left"/>
<img src="{{ base }}/images/cv/proj6/part1/network 20/FirstLayerFilters.png" width="50%" style="float:left"/>

Lowest validation error for above network: 0.436000

---

### Collated Results
The following results are with a learning rate of 0.001 in 50 epochs

**Spatial Resolution**|**Accuracy(Lowest top 1 err)**|**Accuracy(Last top 5 err)**|**Execution Time(secs)**
:-----:|:-----:|:-----:|:-----:
8 |0.494 |0.103 |245.84
10|0.457 |0.094 |241.91
12|0.436 |0.105 |239.26
18|0.413|0.083 |238.09
19|0.417 |0.097 |240.48
20|0.436 |0.085 |240.52

## Learning Rate Experiments

Once the best spatial resolution for the full conv layer was found, the learning rate was chosen by experimenting with multiple values

**Learning Rate**|**Accuracy (Lowest Validation error)**|**Learning Rate Curves**|**First Layer Filters**
:-----:|:-----:|:-----:|:-----:
10<sup>-5</sup> |0.758667 | ![][p1N18lr5TrainingCurves] | ![][p1N18lr5FirstLayerFilters]
10<sup>-4</sup> |0.509333 | ![][p1N18lr4TrainingCurves] | ![][p1N18lr4FirstLayerFilters]
10<sup>-3</sup> |0.412667 | ![][p1N18lr3TrainingCurves] | ![][p1N18lr3FirstLayerFilters]
10<sup>-2</sup> |0.412667 | ![][p1N18lr2TrainingCurves] | ![][p1N18lr2FirstLayerFilters]	
10<sup>-1</sup> |0.412667 | ![][p1N18lr1TrainingCurves] | ![][p1N18FirstLayerFilters]	

## Part 2: Transfer Learning

A noteable feature about deep learning is that networks trained for one task can be repurposed for other (similar)tasks and with minor changes and fine tuning, give good results on the "transferred" tasks.

This can be especially useful when the data set required for a certain task is not enough.

We fine tune and modify the trained VGG network to classify our 15 category dataset

The original VGG-16 Network: ![VGG-16 Network]({{ base }}/images/cv/proj6/vgg16-2.jpeg)

### Architecture 1

The following is the first architecture that I tried for part 2. The architecture is based off the VGG network. To build it, the following changes were made to the base network.

* VGG expects 224 x 224 resolution-ed images
* VGG expects 3 channel images(RGB) so update the input code to repeat the grayscale image in all channels.
* Normalize images
* Replace the existing fully conv layer in the last stage of VGG to return a 1 x 15 vector instead of the 1 x 1000 vector
* Replace the softmax layer (this is done to replace the node values in the pretrained layer)
* Add dropout layers between fc6 - fc7 and fc7 - fc8(i.e. new-fc8)

The results of this architecture follow.

Replace the Fully Convolutional Layer by a new(randomly initialized) FC Layer with output size of 4096 x 15

**Result**

The above network was retrained on the last 9 layers, and an accuracy of the 87.6% was achieved in 5 epochs with a constant learning rate of 0.001

<img src="{{ base }}/images/cv/proj6/part2/22 Layers/trainingCurves.png" width="50%" style="float:left">
<img src="{{ base }}/images/cv/proj6/part2/22 Layers/FirstLayerFilters.png" width="50%" style="float:left">

--- 

### Architecture 1 (with image size jittering)

The above architecture was used with image size jittering to produce the following results:

**Result**

I added some image size jittering in this part, hoping to gain an increase in accuracy, but it didnt affect the results much.

<img src="{{ base }}/images/cv/proj6/part2/22 Layers-img resizejittering/trainingCurves-5epochs.png" width="50%" style="float:left"/>
<img src="{{ base }}/images/cv/proj6/part2/22 Layers-img resizejittering/trainingCurves-10epochs.png" width="50%" style="float:left"/>

Accuracy: 86.87% in 5 epochs and 87.3% in 10 epochs

--- 

### Architecture 2

Tried to add normalization after each conv layer, but failed miserably

**Result**

<center><img src="{{ base }}/images/cv/proj6/part2/arch2-0.01 rate/trainingCurves.png"  width="50%"></center>

Validation Accuracy: 50.6%

--- 

### Architecture 3 with Backprop Depth: 5

Replace the last Fully Convolutional Layer(FC-8) by a new(randomly initialized) FC Layer with output size of 1024 x 15 and replace the (FC-7) by a new(randomly initialized) FC Layer with output size of 4096 x 1024

**Result**

<center><img src="{{ base }}/images/cv/proj6/part2/22 Layers backprop 5/trainingCurves.png"  width="50%"></center>

Validation Accuracy in 5 epochs: 87.7% (756.40 secs)
Validation Accuracy in 4 epochs: 86.2% (545.50 secs)

--- 

### Architecture 3 with Backprop Depth: 6

**Result**

<center><img src="{{ base }}/images/cv/proj6/part2/22 Layers backprop 6/trainingCurves.png"  width="50%"></center>

Validation Accuracy: 86.2% in 4 epochs (537.444951 seconds)

--- 

### Architecture 4

Replaced all fully convolutional layers with 1 fully convolutional layer

**Result**

<center><img src="{{ base }}/images/cv/proj6/part2/17 Layers/trainingCurves.png"  width="50%" ></center>

Validation Accuracy: 82.66% in 5 epochs (588.709 seconds)

---

### Collated Results
*The following results are with a learning rate of 0.001 in 5 epochs*

**Network**                                 |**Accuracy**
:-----:                                     |:-----:
Architecture 1                              |87.6%
Architecture 1(image size jittering)        |86.8%
Architecture 2                              |50.6%
Architecture 3(Backprop Depth 5)            |87.7%
Architecture 3(Backprop Depth 6)            |86.2%
Architecture 4                              |82.6%

---

## Extra credit

### Image sketch token Recognition

We use the data set provided as part of the article: [How Do Humans Sketch Objects?](http://cybertron.cg.tu-berlin.de/eitz/projects/classifysketch/ "ACM Transactions on Graphics (Proc. SIGGRAPH 2012) Mathias Eitz, James Hays and Marc Alexa")

#### Setup

* Download the image dataset from here
    The dataset contains 250 image categories and 80 images in each category.
* The images are named as numbers.
* To split into data and train set, I duplicated the folders and called them train and test.
* In train, I delete all images with odd numbered endings and even numbered endings in test

The following snippet can be used as a shell script to

```sh
cp -r dataset train
mv dataset test

find train/ -name "*1.png" -delete 
find train/ -name "*3.png" -delete 
find train/ -name "*5.png" -delete 
find train/ -name "*7.png" -delete 
find train/ -name "*9.png" -delete 

find test/ -name "*0.png" -delete 
find test/ -name "*2.png" -delete 
find test/ -name "*4.png" -delete 
find test/ -name "*6.png" -delete 
find test/ -name "*8.png" -delete 
```

I added proj6_extra_credit_setup_data.m, proj6_extra_credit.m, proj6_extra_credit_cnn_init.m for the extra credit section.

I used the same VGG fine tuned network from part2 for this task with minor modifications

* **Data Set**: As opposed to the given dataset which had 15 categories with 200 images each, the sketch tokens data set has 250 categories with 80 images each. VGG requires that each image be 224 x 224 pixels. Due to memory constraints I choose 50 categories at random and train/test my network on these categories.
* **Network Changes**: I have used the exact same network from part2, but changed the final layer to predict 250 categories instead of 15.

### Results

Validation Accuracy: 74.75%

Hyper Parameters

* Back prop depth: 5
* Learning Rate: 0.001
* Epochs: 10
* Batch Size: 50


[header]: {{ base }}/images/cv/proj6/header.png
[p1N18TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 18/trainingCurves.png
[p1N18FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 18/FirstLayerFilters.png

[p1N8TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 8/trainingCurves.png
[p1N8FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 8/FirstLayerFilters.png

[p1N10TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 10/trainingCurves.png
[p1N10FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 10/FirstLayerFilters.png

[p1N12TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 12/trainingCurves.png
[p1N12FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 12/FirstLayerFilters.png

[p1N18TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 18/trainingCurves.png
[p1N18FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 18/FirstLayerFilters.png

[p1N19TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 19/trainingCurves.png
[p1N19FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 19/FirstLayerFilters.png

[p1N20TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 20/trainingCurves.png
[p1N20FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 20/FirstLayerFilters.png

[p1N18lr5TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 18 - lr -5/trainingCurves.png
[p1N18lr5FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 18 - lr -5/FirstLayerFilters.png	

[p1N18lr4TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 18 - lr -4/trainingCurves.png
[p1N18lr4FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 18 - lr -4/FirstLayerFilters.png	

[p1N18lr3TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 18 - lr -3/trainingCurves.png
[p1N18lr3FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 18 - lr -3/FirstLayerFilters.png	

[p1N18lr2TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 18 - lr -2/trainingCurves.png
[p1N18lr2FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 18 - lr -2/FirstLayerFilters.png	

[p1N18lr1TrainingCurves]: {{ base }}/images/cv/proj6/part1/network 18 - lr -1/trainingCurves.png
[p1N18lr1FirstLayerFilters]: {{ base }}/images/cv/proj6/part1/network 18 - lr -1/FirstLayerFilters.png	
