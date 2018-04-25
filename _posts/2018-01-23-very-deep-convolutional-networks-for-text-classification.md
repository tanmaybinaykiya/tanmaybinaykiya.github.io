---
layout:     post
title:      Very Deep Convolutional Networks for Text Classification 
date:       2017-12-22 14:12:22
summary:    Very Deep Convolutional Networks for Text Classification 
categories: natural-language machine-learning deep-learning text-processing text-classification neural-nets convolutional-neural-networks vgg sentiment-analysis topic-classification 
tags:       paper-summary blogpost
---

Alexis Conneau; Holger Schwenk; Loïc Barrault; Yann Lecun  
[Arxiv Link](https://arxiv.org/abs/1606.01781)  
Presented at [EACL 2017](http://www.aclweb.org/anthology/E/E17/)

Computer Vision has been successful by using Deep Convolutional networks, while the state of the art for natural language tasks are recurrent neural networks (particularly LSTMs) which are shallow. The paper discusses a new approach to build deep networks for text processing. The paper shows its improvements over the state of the art by using up to 29 layered CNNs. 

## Introduction

The paper discusses a new approach to build deep networks for text processing. The paper shows its improvements over the state of the art by using up to 29 layered CNNs. The paper argues that LSTMs are generic learning machines for sequence processing which are lacking task specific structure. The success of CNNs in regards to Computer Vision are attributed to the hierarchical representations that the layers capture. Texts have similar properties, characters combine to form n-grams, stems, words, phrase, sentences etc. Through the succesful use of deep convoultional layers in text classification, this paper encourages the community to invest in deep networks.

## Related work and learnings from them
The paper walks through the previous successful applications of convolutional networks in NLP. They discuss the typical features that have been used, namely the Bag of Words, n-grams and their TF-IDF. 

Word embeddings in low-dim space - like element wise mean - criticism: all notion of token order is disregarded 

Another Approach: Recursive Neural Networks, A parser determines the order in which the word embeddings are combined. Recurrent Neural Networks are a special case of recursive NNs. 

### CNNs in NLP
All approaches operate on words projected in a high dimensional space

* Kim 2014
    * Architecture: Conv Layer(multiple widths and filters) - Max pooling layer over time - Full Conv (with dropout) 
* Kalchbrenner 2014
    * Architecture: 5 Conv layers similar to Kim 2014
    * Salient Features:
        * Multiple temporal k-max pooling layers. Detects k most important features in a sentence, independent of their specific position, preserving relative order
* Zhang 2015
    * Performed sentiment analysis entirely at character level
    * 6 conv layers(kernels of size 3 and 7)  - 3 FC layers - WE USE THE SAME SETTING
* Yang 2016
    * Hierarchical attention based network for doc classification - performs an attention first on the sentence in the document and on the words in the sentence
* Xiao & Cho, 2016
    * Combines RNNs and CNNs
    * Conv Net with upto 5 layers- serves as an input for an LSTM
    * No network with VGG like layers or deeper than 6 conv layers

Further, The paper criticizes the use of word embeddings in low dimensional space. They further study the salient features of papers that they derived inspiration from in their approach in terms of architecture and feature embeddings.

## Architecture 
The architecture of the network that the paper discusses is explained further. 
* The paper uses a lookup table that generates a 2D tensor of size (f<sub>0</sub>, s) that contain the embeddings of the s characters. S is fixed to 1024 and f<sub>0</sub> can be seen as the RGB in images
* 1 Layer of 64 Conv of size 3
* They use a stack of temporal <b>convolutional blocks</b>
* The architecture follows the design rules of the VGG network
    * For the same output temporal resolution, the layers have the same number of features maps
    * On halving the temporal resolution, the number of feature maps is doubled to reduce the memory footprint of the network
* They claim that output of the pooling operations after the convolutional layers, represent a high level representation of the input text (Probably missing in the paper: How do they confirm this?)
* Other shallow convolutional architectures have fixed temporal resolutions, but with multiple smaller stacked convolutions, the network learns how to best combine the features
* For the classification tasks in this work, the temporal resolution of the output of the convolution blocks is first down-sampled to a fixed dimension using k-max pooling. By these means, the network extracts the k most important features, independently of the position they appear in the sentence
* The architecture does not use any dropout in the fully convolutional layers
* They divide the network into convolutional blocks which contain convolutional layers of small filter size. The small filter size makes it feasible to do so as we go deeper in the network
* The paper explores 3 types of down-sampling blocks
    * ResNet-like Conv layer of stride 2
    * Kalchbrenner-like Max-Pool: K-max pooling layer, k such that resolution is halved
    * VGG-like Max-Pool: Max pooling with kernel size 3 and stride 2

The paper attriutes the low number of classes(as compared to 1000 in ImageNet) in Natural language to the  difficulty in train deep architectures due on account of loss of gradient information

## Experimental Setup

* The low number of classes(as compared to 1000 in ImageNet) in Natural language makes it difficult to train deep architectures due to loss in gradient information
* The only preprocessing that is applied is lower casing
* The model was trained with Stochastic Gradient Descent with a minibatch size of 128, learning rate 0.01 and momentum 0.9

## Results  
The main contribution that paper wishes to make through evaluation of networks of increasing depth using an architecture with small temporal convolution filters with different types of pooling, is that a significant improvement can be achieved on the state-of-the-art configurations using deep networks

## Highlighted conclusions of the paper 
* Deep architectures work well on big data sets in particular, even for small depths
* Max pooling performs better than other pooling types
* The proposed deep network is better or at least comparable performance on large datasets
* Going even deeper degrades accuracy. Short-cut connections help reduce the degradation
* With increasing depth, accuracy saturates and starts degrading rapidly due to **vanishing gradients**. This is solved using ResNet like shortcut connections

## Summary

* What are the labels, and how were they obtained?
    * The paper uses multiple datasets in the evaluation of their approach. The labels for large-scale text classification are as follows

| Dataset |  Purpose | 
| ------------- |:-------------:| 
| AG’s news              | English news categorization |
| Sogou news             | Chinese news categorization | 
| DBPedia                | Ontology classification     | 
| Yelp Review Polarity   | Sentiment analysis          | 
| Yelp Review Full       | Sentiment analysis          | 
| Yahoo! Answers         | Topic classification        | 
| Amazon Review Full     | Sentiment analysis          | 
| Amazon Review Polarity | Sentiment analysis          | 

* Why is it interesting/useful to predict these labels?
    * Sentiment analysis can extremely useful for corporates to judge products and decide strategies
    * Topic classification is one of the first steps required to improve natural language bots’ context learning 

* What classifier(s) do they use, and the reasons behind their choice? Do they use linear classifiers like the ones in this problem set?
    * They use Neural Networks for the task. Neural networks may be considered linear classifiers in the sense of the linear weight matrices that they used to compute the convolutions. At the same time, they have non linearities to build complex functions
    
* What features do they use? Explain any features outside the bag-of-words model, and why they used them.
    * They use a combination of Bag of words, N-grams and TF-IDF

* What is the conclusion of the paper? Do they compare between classifiers, between feature sets, or on some other dimension?
    * The paper presents a new approach for document classification using deep neural networks.

* Give a one-sentence summary of the message that they are trying to leave for the reader.

    * The idea of using Deep Convolutional Nets for Text Processing is to harness the potential of deep networks which have been extremely successful in Computer Vision and use them for Natural Language tasks.