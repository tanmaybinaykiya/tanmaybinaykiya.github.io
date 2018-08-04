---
layout:     post
title:      Sequence labelling
date:       2018-04-18 18:04:18
summary:    Sequence labelling is the task of assigning labels to each token in a sequence. A common application of sequence labelling is Part of Speech tagging - assigning parts of speech to each word in a sentence. In this project we iterate through multiple methods, eventually building a state-of-the-art model for the task using Deep Learning
categories: natural-language machine-learning deep-learning sequence-labelling neural-nets recurrent-neural-networks long-short-term-memory-network lstm pos-tag part-of-speech tagging computational-modeling data-models Neural-networks Training viterbi hidden-markov-model georgia-tech gatech 
cover-image: /images/nlp/seq-labeling/cover.png
---

<figure><img style="width:100%" src="{{base}}/images/nlp/seq-labeling/cover.png"/></figure>

Sequence labelling is the task of assigning tags to each token in a sequence. The tokens and tags can be absolutely anything. A common application of sequence labelling is Part of Speech tagging - assigning parts of speech to each word in a sentence. 

## Sequence labelling as a classification problem

> ### Classification  
<br/> Classification is the task of assigning labels to your input and finds applications in all realms of Machine Learning.   
Scene Classification, Optical Character Recognition, Spam Detection, Sentiment Analysis are all modeled as classification problems
To generalize all such problems we standardize it as follows.  
<br/>For every input, we need only it's characteristic features which distinguish the positive inputs from the non-positive ones. For example if all our inputs are animals and we wish to distinguish bipeds from quadrupeds, the distinguishing feature is the legs. Given an image of an animal the **feature function** should be able to focus on the legs and learn to count them. i.e. we need a function that takes as input the classification object and returns it's features.  
We next need a weight function $$ g(y) $$ that is a function of the feature function and returns a boolean that says whether the example is a positive one or not.  
<br/>For computational tractablity, the feature function $$ f(x) $$ is often defined such that it returns a matrix and the $$ g(y) $$ is the inner product of a weight matrix $$ \theta $$ with the feature matrix. The product of this composition gives a confidence score of the classification that we made.
<br/><center>$$ score = \theta \cdot f(x) $$ </center>

Sequence labelling can be modeled as a classification problem, where each tag is a class that you want to classify each token into.   
We write the feature function $$ f ((\overline{w}, m), y) $$ to indicate the feature function for applying tag y (the label) to word $$ w_m $$ in the sequence $$ w_1, w_2, \ldots, w_M $$    
  
How to build such a feature function?   

### Naïve approach  
<br/>
$$ f((w = {they\ can\ fish}, m = 1), N) = \langle they, N \rangle $$  
$$ f((w = {they\ can\ fish}, m = 2), V) = \langle can, V \rangle $$  
$$ f((w = {they\ can\ fish}, m = 3), V) = \langle fish, V \rangle $$  

At a vocab size of V and K tags, there are V x K features. Each of these features must be assigned a weight. The weights can be learned from a labelled dataset using classification algorithms such as perceptron but this isn’t necessary in this case: it would be equivalent to define the classification weights directly, with $$\theta_{w, y} = 1$$ for the tag y most frequently associated with word w, and $$\theta_{w, y} = 0$$ for all other tags   
This goes wrong when the real tag is not the most frequent tag for the word in the training set. So we add context (neighboring words).  

$$ f((w = they\ can\ fish, m=1), N) = \{ \langle w_i=they, y_i = N\rangle,  \langle w_{i-1}=\Diamond, y_i = N \rangle,      \langle w_{i+1}=can,     y_i = N \rangle \} $$  
$$ f((w = they\ can\ fish, m=2), N) = \{ \langle w_i=can,  y_i = V\rangle,  \langle w_{i-1}=they,     y_i = V \rangle,      \langle w_{i+1}=fish,    y_i = V \rangle \} $$  
$$ f((w = they\ can\ fish, m=3), V) = \{ \langle w_i=fish, y_i = V\rangle,  \langle w_{i-1}=can,      y_i = V \rangle,      \langle w_{i+1}=\square, y_i = V \rangle \} $$   

However, even with this enhanced feature set, it may be difficult to tag some sequences correctly. One reason is that there are often relationships between the tags themselves.   
For example, in English it is relatively rare for a verb to follow another verb — particularly if we differentiate modal verbs like **can** and **should** from more typical verbs, like *give*, *take* etc. We would like to incorporate preferences against such tag sequences, and preferences for other tag sequences, such as NOUN-VERB. 

> ### Garden path sequence
Consider the sentence **The old man the boat**  
On the first glance, the sentence seems incomplete. But when you read it again, you realize that it is in fact a complete sentence.  
Grammatically, the word *the* is a DETERMINER.   
When you read the sentence, what part of speech did you first assign to old? Typically, this word is an ADJECTIVE — abbreviated as J — which is a class of words that modify nouns.  
Similarly, man is usually a NOUN. The resulting sequence of tags is D J N D N. But this is a mistaken “garden path” interpretation, which ends up leading nowhere.  
It is unlikely that a DETERMINER would directly follow a NOUN and particularly unlikely that the entire sentence would lack a verb. The only possible VERB in the sentence is the word man, which can refer to the act of maintaining and piloting something — often boats. But if man is tagged as a VERB, then old is seated between a DETERMINER and a VERB, and must be a NOUN. And indeed, ADJECTIVEs can often have a second interpretation as NOUNs when used in this way (e.g., the young, the restless).  
This reasoning, in which the labelling decisions are intertwined, cannot be applied in a setting where each tag is produced by an independent classification decision. 

## Sequence labelling as structure prediction 
*The entire sequence of tags as a label itself*

In stead of making a classifiction decision for each token, we now make a decision for the entire sentence.  
For a sequence of words $$ \underline{w}_{1:M} := (w_1, w_2, \ldots, w_M) $$ there is a set of possible taggings $$ \Upsilon ({w}_{1:M}) = \Upsilon^M \ {}$$ where   
$$ \Upsilon $$ refers to the set of individual tags and   
$$ \Upsilon^M $$ refers to the set of tag sequences of length M. 

Hence the sequence labelling problem can be treated as a classification problem in the label space $$ \Upsilon(w_{1:M}) $$  

$$ \hat{y}_{1:M} = argmax_{y_{1:M \in \Upsilon(w_{1:M})}} \theta \cdot f(\overline{w}_{1:M}, y_{1:M}) $$ where   
$${y}_{1:M}$$ is a sequence of M tags  

Note that in this formulation, we have a feature function that consider the entire tag sequence $$ y_m $$ . Such a feature function can therefore include features that capture the relationships between tagging decisions, such as the preference that determiners not follow nouns, or that all sentences have verbs. 

## Tractability

POS tag sets range from 10 to few hundreds. For a sequence length of 20, the permutations of tag sequences of length 20 is  for a POS set of 10. Enumerating and scoring each sequence is hence intractable.

However, the situation changes when we restrict the feature function. Suppose we choose features that never consider more than one tag 

$$ f(\underline{w},\underline{y}) =  \sum_{m=1}^{M} f(\underline{w},y_m,m) $$

<!-- {% gist 5997ae8cd51f160b80c0debcf4ade94a %} -->

## References

* [Prof. Jacob Eisenstein's Notes](https://github.com/jacobeisenstein/gt-nlp-class/blob/master/notes/eisenstein-nlp-notes.pdf)
