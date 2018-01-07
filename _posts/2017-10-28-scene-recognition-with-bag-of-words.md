---
layout:     post
title:      Scene Recognition with Bag of Words
date:       2017-10-28 17:10:28
summary:    Scene Recognition pipeline based on Bag of Words as a feature descriptor
categories: computer-vision image-classification image-representation natural-scenes unsupervised-learning  learning-natural-scene-category training-set unsupervised-learning Animals Cities-and-towns Dictionaries Frequency Histograms Humans Layout Unsupervised-learning Vehicles knn k-nearest-neighbour matlab support-vector-machines bag-of-words
tags:       project
---
<center><img src="{{ base }}/images/cv/proj4/header.png" /><br>An example of a typical bag of words classification pipeline. Figure by <a href="http://www.robots.ox.ac.uk/~vgg/research/encoding_eval/">Chatfield et al.</a></center>

> This project report was compiled as part of the course requirements of [CS 6476 Computer Vision](https://www.cc.gatech.edu/~hays/compvision/) under [Prof. James Hays](https://www.cc.gatech.edu/~hays/)

## Objective
Classify a given set of images into a predefined set of scenes

## Abstract Algorithm
A set of training images classified by their scenes is used to train a model to find a classification model. Once the model is obtained it is tested on the test set to verify the model.

### Step 1
The images are represented using pixel values and a meaningful representation of these images is required to classify these images. We implement the tiny image representation and the bags of sift representation

#### **Tiny Image Representation**
For a baseline implementation we obtain a 16x16 pixel representation of the image and normalise it. The resized image is then vectorized and used as the feature representing the image

### Step 2
For a baseline implementation of the classifier, we use the K-Nearest-Neighbour Algorithm

#### **K-Nearest-Neighbour Algorithm**
Given a feature of the image and a set of features of pre-classified images, we use the label of the K-Nearest neighbours in the feature space to decide the label for the given image.

### Step 3
For better implementation of the classifier, we use Support Vector Machine Method

#### **Support Vector Machine**
At a broad-level, given a binary classification of data, Support Vector Machine finds a hyper plane that partitions the data.

For Image classification, we implement a One-Vs-All method i.e. For every class we find the hyper plane that partitions the image features as class and Not class. We hence have as many hyper planes as the number of classes that we classify into. The dot product of the hyperplane with the image feature represents the distance of the data from the partition and hence gives a rough estimate on the confidence. We use this information to determine the actual class by choosing the class whose hyper plane is the farthest from the image feature representation

### Step 4
#### **Bag of words**
A fixed size of vocabulary of image features(SIFT in our case) is built using the existing training images.

For simplicity, points are sampled at a fixed distance(step size) and the SIFT representation is computed for each of them. All such SIFT features across all images are then passed through a K-Means algorithm to find a fixed number(K=vocab_size) of set of features. The set of these K vectors is to be used as the vocabulary here after.

For each training image, SIFT vectors are computed at a fixed step size(Need not be the same as that used for the vocabulary).

A histogram is computed for each image and this serves as the feature representation of the image. The histogram signifies the number of features of the image that are closest to each of the vocabulary features.

_The algorithm performs better with lower step sizes but due to hardware constraints a step size of 4 with SIFT and 10 with SIFT + GIST was the lowest achievable_


## Dataset Used

We use the 15 scene dataset introduced in [Lazebnik et al. 2006](http://www.di.ens.fr/willow/pdfs/cvpr06b.pdf), although built on top of previously published datasets



## Regularization Parameter Tuning

Regularization is employed to reduce the overfitting nature of the hyperplane. Lambda parametrizes the regularization in SVMs

|-----------|----------|
| Lambda	| Accuracy |
|-----------|----------|
| 0.0000001	| 0.642    |
| 0.000001	| 0.645    |
| 0.00001	| 0.638    |
| 0.0001	| 0.641    |
| 0.001	    | 0.643    |
| 0.01	    | 0.647    |
| 0.1	    | 0.595    |
| 1	        | 0.609    |
| 10	    | 0.626    |
| 100	    | 0.573    |
| 1000	    | 0.435    |
| 10000	    | 0.405    |
| 100000	| 0.405    |
|-----------|----------|

<center><img src="{{ base }}/images/cv/proj4/svm_lambda_vs_accuracy.png" /></center>

## Extra Credit

### Primal SVM

As opposed to the conventional SVM, primal SVMs use the newtonian quadratic method to converge to the solution. This results in a faster convergence and more accurate hyperplane. [Olivier Chapelle's MATLAB code](http://olivier.chapelle.cc/primal/) works well.

|-----------|----------|
| Lambda	| Accuracy |
|-----------|----------|
| 0.0001	| 0.429    |
| 0.001	    | 0.431    |
| 0.01	    | 0.434    |
| 0.1	    | 0.434    |
| 1	        | 0.542    |
| 10	    | 0.586    |
| 100	    | 0.667    |
| 250	    | 0.679    |
| 500	    | 0.685    |
| 750	    | 0.679    |
| 1000	    | 0.679    |
| 1500	    | 0.663    |
| 10000	    | 0.570    |
| 100000	| 0.082    |
|-----------|----------|

<center><img src="{{ base }}/images/cv/proj4/primal_svm_lambda_vs_accuracy.png" /></center>

### GIST Descriptor 

Whereas SIFT provides a descriptor at a pixel level, [GIST](#references)(summary) descriptor provides a high level representation of the entire image.

To use the GIST vector in our algorithm, we find the GIST representation of the image and concat it with each of the SIFT descriptors. This feature representationis used to build the vocabulary and for classifying the test image set.

**Results**
The performance of the pipeline improved from 65% to 68% on switching to SIFT + GIST

### ~~Vocabulary Size Variation~~

~~Although it is expected that the accuracy is proportional to the vocabulary size upto a limit after which it converges, I received an unexpected result. The vocabulary size did not affect the accuracy at all. I have uploaded the vocabulary vectors in the upload package.~~

## Results

### Accuracy

|--------------------------------------|----------|
| Pipeline                             | Accuracy |
|--------------------------------------|----------|
| Tiny Image and K-Nearest-Neighbour   |  0.19    |
| Bags of Sift and K-Nearest-Neighbour |  0.51    |
| Bags of SIFT and SVM                 |  0.68    |
| Bags of SIFT+GIST and PRIMAL SVM     |  0.70    | 
|--------------------------------------|----------|

### Parameters
 
Lambda Value for SIFT and SVM: 0.01
Lambda Value for SIFT+GIST and Primal SVM: 500

Scene Classification Results Visualization

<center><img src="{{ base }}/images/cv/proj4/confusion_matrix.png" /><br>Accuracy (mean of diagonal of confusion matrix) is 0.700</center>


<table border="0" cellpadding="4" cellspacing="1">
    <tbody>
        <tr>
            <th>Category name</th>
            <th>Accuracy</th>
            <th colspan="2">Sample training images</th>
            <th colspan="2">Sample true positives</th>
            <th colspan="2">False positives with true label</th>
            <th colspan="2">False negatives with wrong predicted label</th>
        </tr>
        <tr>
            <td>Kitchen</td>
            <td>0.520</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Kitchen_image_0083.jpg" width="100" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Kitchen_image_0066.jpg" width="107" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Kitchen_image_0171.jpg" width="100" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Kitchen_image_0052.jpg" width="115" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Industrial_image_0038.jpg" width="94" height="75"><br><small>Industrial</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Bedroom_image_0095.jpg" width="101" height="75"><br><small>Bedroom</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Kitchen_image_0168.jpg" width="112" height="75"><br><small>Industrial</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Kitchen_image_0134.jpg" width="113" height="75"><br><small>LivingRoom</small></td>
        </tr>
        <tr>
            <td>Store</td>
            <td>0.730</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Store_image_0103.jpg" width="106" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Store_image_0279.jpg" width="103" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Store_image_0010.jpg" width="104" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Store_image_0081.jpg" width="131" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/OpenCountry_image_0084.jpg" width="75" height="75"><br><small>OpenCountry</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/InsideCity_image_0084.jpg" width="75" height="75"><br><small>InsideCity</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Store_image_0008.jpg" width="89" height="75"><br><small>InsideCity</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Store_image_0057.jpg" width="100" height="75"><br><small>Office</small></td>
        </tr>
        <tr>
            <td>Bedroom</td>
            <td>0.560</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Bedroom_image_0058.jpg" width="93" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Bedroom_image_0001.jpg" width="101" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Bedroom_image_0021.jpg" width="113" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Bedroom_image_0074.jpg" width="116" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/LivingRoom_image_0004.jpg" width="100" height="75"><br><small>LivingRoom</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/LivingRoom_image_0008.jpg" width="100" height="75"><br><small>LivingRoom</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Bedroom_image_0138.jpg" width="52" height="75"><br><small>Office</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Bedroom_image_0050.jpg" width="113" height="75"><br><small>LivingRoom</small></td>
        </tr>
        <tr>
            <td>LivingRoom</td>
            <td>0.630</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/LivingRoom_image_0199.jpg" width="100" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/LivingRoom_image_0139.jpg" width="94" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/LivingRoom_image_0123.jpg" width="98" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/LivingRoom_image_0088.jpg" width="100" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/InsideCity_image_0069.jpg" width="75" height="75"><br><small>InsideCity</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Suburb_image_0103.jpg" width="113" height="75"><br><small>Suburb</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/LivingRoom_image_0110.jpg" width="100" height="75"><br><small>Bedroom</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/LivingRoom_image_0055.jpg" width="100" height="75"><br><small>Kitchen</small></td>
        </tr>
        <tr>
            <td>Office</td>
            <td>0.770</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Office_image_0141.jpg" width="102" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Office_image_0197.jpg" width="119" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Office_image_0100.jpg" width="134" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Office_image_0040.jpg" width="103" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Industrial_image_0125.jpg" width="46" height="75"><br><small>Industrial</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Kitchen_image_0028.jpg" width="57" height="75"><br><small>Kitchen</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Office_image_0181.jpg" width="113" height="75"><br><small>LivingRoom</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Office_image_0117.jpg" width="98" height="75"><br><small>LivingRoom</small></td>
        </tr>
        <tr>
            <td>Industrial</td>
            <td>0.490</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Industrial_image_0257.jpg" width="100" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Industrial_image_0217.jpg" width="100" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Industrial_image_0083.jpg" width="106" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Industrial_image_0139.jpg" width="55" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/InsideCity_image_0016.jpg" width="75" height="75"><br><small>InsideCity</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Street_image_0145.jpg" width="75" height="75"><br><small>Street</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Industrial_image_0084.jpg" width="112" height="75"><br><small>Kitchen</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Industrial_image_0029.jpg" width="63" height="75"><br><small>Store</small></td>
        </tr>
        <tr>
            <td>Suburb</td>
            <td>0.930</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Suburb_image_0089.jpg" width="113" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Suburb_image_0144.jpg" width="113" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Suburb_image_0084.jpg" width="113" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Suburb_image_0168.jpg" width="113" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Coast_image_0087.jpg" width="75" height="75"><br><small>Coast</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/OpenCountry_image_0076.jpg" width="75" height="75"><br><small>OpenCountry</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Suburb_image_0117.jpg" width="113" height="75"><br><small>LivingRoom</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Suburb_image_0120.jpg" width="113" height="75"><br><small>Store</small></td>
        </tr>
        <tr>
            <td>InsideCity</td>
            <td>0.610</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/InsideCity_image_0090.jpg" width="75" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/InsideCity_image_0030.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/InsideCity_image_0040.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/InsideCity_image_0039.jpg" width="75" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/TallBuilding_image_0027.jpg" width="75" height="75"><br><small>TallBuilding</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Industrial_image_0054.jpg" width="100" height="75"><br><small>Industrial</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/InsideCity_image_0061.jpg" width="75" height="75"><br><small>Store</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/InsideCity_image_0017.jpg" width="75" height="75"><br><small>Kitchen</small></td>
        </tr>
        <tr>
            <td>TallBuilding</td>
            <td>0.680</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/TallBuilding_image_0234.jpg" width="75" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/TallBuilding_image_0066.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/TallBuilding_image_0004.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/TallBuilding_image_0082.jpg" width="75" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/LivingRoom_image_0043.jpg" width="100" height="75"><br><small>LivingRoom</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Mountain_image_0017.jpg" width="75" height="75"><br><small>Mountain</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/TallBuilding_image_0053.jpg" width="75" height="75"><br><small>InsideCity</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/TallBuilding_image_0016.jpg" width="75" height="75"><br><small>Industrial</small></td>
        </tr>
        <tr>
            <td>Street</td>
            <td>0.750</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Street_image_0015.jpg" width="75" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Street_image_0257.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Street_image_0018.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Street_image_0007.jpg" width="75" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/OpenCountry_image_0041.jpg" width="75" height="75"><br><small>OpenCountry</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/TallBuilding_image_0035.jpg" width="75" height="75"><br><small>TallBuilding</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Street_image_0133.jpg" width="75" height="75"><br><small>Industrial</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Street_image_0038.jpg" width="75" height="75"><br><small>Store</small></td>
        </tr>
        <tr>
            <td>Highway</td>
            <td>0.810</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Highway_image_0028.jpg" width="75" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Highway_image_0106.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Highway_image_0099.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Highway_image_0085.jpg" width="75" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/OpenCountry_image_0058.jpg" width="75" height="75"><br><small>OpenCountry</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Mountain_image_0001.jpg" width="75" height="75"><br><small>Mountain</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Highway_image_0088.jpg" width="75" height="75"><br><small>InsideCity</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Highway_image_0036.jpg" width="75" height="75"><br><small>Coast</small></td>
        </tr>
        <tr>
            <td>OpenCountry</td>
            <td>0.580</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/OpenCountry_image_0390.jpg" width="75" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/OpenCountry_image_0089.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/OpenCountry_image_0059.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/OpenCountry_image_0017.jpg" width="75" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Forest_image_0096.jpg" width="75" height="75"><br><small>Forest</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Mountain_image_0119.jpg" width="75" height="75"><br><small>Mountain</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/OpenCountry_image_0071.jpg" width="75" height="75"><br><small>Highway</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/OpenCountry_image_0079.jpg" width="75" height="75"><br><small>Forest</small></td>
        </tr>
        <tr>
            <td>Coast</td>
            <td>0.770</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Coast_image_0111.jpg" width="75" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Coast_image_0109.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Coast_image_0064.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Coast_image_0130.jpg" width="75" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Highway_image_0036.jpg" width="75" height="75"><br><small>Highway</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Mountain_image_0005.jpg" width="75" height="75"><br><small>Mountain</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Coast_image_0004.jpg" width="75" height="75"><br><small>Highway</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Coast_image_0030.jpg" width="75" height="75"><br><small>OpenCountry</small></td>
        </tr>
        <tr>
            <td>Mountain</td>
            <td>0.830</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Mountain_image_0313.jpg" width="75" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Mountain_image_0074.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Mountain_image_0109.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Mountain_image_0043.jpg" width="75" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/OpenCountry_image_0101.jpg" width="75" height="75"><br><small>OpenCountry</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Coast_image_0031.jpg" width="75" height="75"><br><small>Coast</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Mountain_image_0005.jpg" width="75" height="75"><br><small>Coast</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Mountain_image_0115.jpg" width="75" height="75"><br><small>Highway</small></td>
        </tr>
        <tr>
            <td>Forest</td>
            <td>0.840</td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Forest_image_0279.jpg" width="75" height="75"></td>
            <td bgcolor="LightBlue"><img src="{{ base }}/images/cv/proj4/thumbnails/Forest_image_0116.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Forest_image_0022.jpg" width="75" height="75"></td>
            <td bgcolor="LightGreen"><img src="{{ base }}/images/cv/proj4/thumbnails/Forest_image_0057.jpg" width="75" height="75"></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Mountain_image_0100.jpg" width="75" height="75"><br><small>Mountain</small></td>
            <td bgcolor="LightCoral"><img src="{{ base }}/images/cv/proj4/thumbnails/Store_image_0073.jpg" width="101" height="75"><br><small>Store</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Forest_image_0128.jpg" width="75" height="75"><br><small>Street</small></td>
            <td bgcolor="#FFBB55"><img src="{{ base }}/images/cv/proj4/thumbnails/Forest_image_0109.jpg" width="75" height="75"><br><small>Mountain</small></td>
        </tr>
    </tbody>
</table>





## References

1 [Oliva, A. & Torralba, A. International Journal of Computer Vision (2001) 42: 145.](https://doi.org/10.1023/A:1011139631724)
2 [L. Fei-Fei and P. Perona, "A Bayesian hierarchical model for learning natural scene categories," 2005 IEEE Computer Society Conference on Computer Vision and Pattern Recognition (CVPR'05), 2005, pp. 524-531 vol. 2.](http://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=1467486&isnumber=31473)
