---
layout:     post
title:      Automatic image processing using Expectation Maximization with Gaussian Mixture Models
date:       2017-11-13 17:11:13
summary:    Automatic image processing is a key component to many AI systems, including facial recognition and video compression. One basic method for processing is segmentation, by which we divide an image into a fixed number of components in order to simplify its representation. For example, we can train a mixture of Gaussians to represent an image, and segment it according to the simplified representation as shown in the images below. We perform austomatic image segmentation using Gaussian Mixture Models
categories: computer-vision gaussian-mixture-models image-processing image-segmentation expectation-maximization
cover-image: 
---

Automatic image processing is a key component to many AI systems, including facial recognition and video compression. One basic method for processing is segmentation, by which we divide an image into a fixed number of components in order to simplify its representation. For example, we can train a mixture of Gaussians to represent an image, and segment it according to the simplified representation as shown in the images below.

We build a Gaussian mixture model to be trained with expectation-maximization.

A Gaussian mixture model is a generative model for representing the underlying probability distribution of a complex collection of data, such as the collection of pixels in a grayscale photograph. In the context of this problem, a Gaussian mixture model defines the joint probability f(x) as

<center>$$ f(x) = \sum_{i=1}^{k} m_iN_i(x|\mu_i, \sigma_i^2) $$  </center>

where $$ x \in [0,1] $$ is a grayscale value, f(x) is the joint probability of that gray scale value, $$ m_i $$ is the mixing coefficient on component i, $$ N_i $$ is the i-th Gaussian distribution underlying the value x with mean $$ \mu_i $$ and variance $$ \sigma_i^2 $$.  

We will be using this model to segment photographs into different grayscale regions. The idea of segmentation is to assign a component i to each pixel x using the maximum posterior probability

$$ \text{component}_x = \text{argmax}_i (m_i N_i (x|\mu_i, \sigma_i^2)) $$

Then we will replace each pixel in the image with its corresponding $$ \mu_i $$ to produce a result as below 


<figure>
  <img class="img-half-width" src="/images/em/party_spock.png" /><img class="img-half-width" src="/images/em/party_spock3.png" />
  <figcaption>Original on the left. Segmented with three components on the right</figcaption>
</figure>

## Implementation of Gaussian Mixture Model

- Calculate the joint log probability of a given grayscale value
- Use expectation-maximization (EM) to train the model to represent the image as a mixture of Gaussians. 
To initialize EM, we set each component's mean to the grayscale value of randomly chosen pixel and variance to 1, and the mixing coefficients to a uniform distribution. 
We set the convergence condition in default_convergence(): if the new likelihood is within 10% of the previous likelihood for 10 consecutive iterations, the model has converged.
- Calculate the log likelihood of the trained model
- Segment the image according to the trained model
- Determine the best segmentation by iterating over model training and scoring, since EM isn't guaranteed to converge to the global maximum. (5 points)

## Bayesian Information Criterion

Our criterion for choosing a model was whether it maximizes the posterior likelihood regardless of how many parameters this requires. As a result, the "best" model may simply be the model with the most parameters, which would overfit to the training data.
To avoid overfitting, we can use the Bayesian information criterion (a.k.a. BIC) which penalizes models based on the number of parameters they use. In the case of the Gaussian mixture model, this is equal to the number of components times the number of variables per component (mean, variance and mixing coefficient) = 3 x components.

{%gist ac941ede9a1e273de64d204324be1d7d %}

