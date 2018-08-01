---
layout:     post
title:      Sequence Labeling
date:       2018-04-18 18:04:18
summary:    Sequence Labeling is the task of assigning labels to each token in a sequence. A common application of sequence labelling is Part of Speech tagging - assigning parts of speech to each word in a sentence. In this project we iterate through multiple methods, eventually building a state-of-the-art model for the task using Deep Learning
categories: natural-language machine-learning deep-learning sequence-labelling neural-nets recurrent-neural-networks long-short-term-memory-network lstm pos-tag part-of-speech tagging computational-modeling data-models Neural-networks Training viterbi hidden-markov-model georgia-tech gatech 
cover-image: 
---

<!-- > This project was completed as part of the course requirements of [CS 7650 Natural Language](https://github.com/jacobeisenstein/gt-nlp-class) under [Prof. Jacob Eisenstein](https://www.cc.gatech.edu/~jeisenst/)   -->

Sequence Labeling is the task of assigning tags to each token in a sequence. The tokens and tags can be absolutely anything. A common application of sequence labelling is Part of Speech tagging - assigning parts of speech to each word in a sentence. 

## Sequence labeling as classification

Sequence labeling can be modeled as a classification problem, where each tag is a class that you want to classify each token into. 
We can write  $$ f ((\underline{w}, m), y) $$ to indicate the feature function for applying tag y to word $$ w_m $$ in the sequence $$ w_1, w_2, \ldots, w_M $$  
How to build such a feature function?  

**Naïve approach** 

$$ f((w = they can fish, m = 1), N) = <they, N>  $$
$$ f((w = they can fish, m = 2), V) = <can, V>  $$
$$ f((w = they can fish, m = 3), V) = <fish, V>  $$

At a vocab size of V and K tags, there are V x K features. Each of these features must be assigned a weight. The weights can be learned from a labelled dataset using classification algorithms such as perceptron but this isn’t necessary in this case: it would be equivalent to define the classification weights directly, with $$\theta_{w, y} = 1$$ for the tag y most frequently associated with word w, and $$\theta_{w, y} = 0$$ for all other tags   
This goes wrong when the real tag is not the most frequent tag for the word in the training set. So we add context (neighboring words).  

$$ f((w = they\ can\ fish, m=1), N) = {<w_i=they, y_i = N>, <w_{i-1}=\diamond, y_i = N>, <w_{i+1}=can, y_i = N>} $$
$$ f((w = they\ can\ fish, m=2), N) = {<w_i=can, y_i = V>, <w_{i-1}=they, y_i = V>, <w_{i+1}=fish, y_i = V>} $$
$$ f((w = they\ can\ fish, m=3), V) = {<w_i=fish, y_i = V>, <w_{i-1}=can, y_i = V>, <w_{i+1}=\Diamond, y_i = V>} $$ 

However, even with this enhanced feature set, it may be difficult to tag some sequences correctly. One reason is that there are often relationships between the tags themselves. 
For example, in English it is relatively rare for a verb to follow another verb — particularly if we differentiate modal verbs like **can** and **should** from more typical verbs, like *give*, *take* etc. We would like to incorporate preferences against such tag sequences, and preferences for other tag sequences, such as NOUN-VERB. 

> Garden path sequence: The old man the boat  
Grammatically, the word the is a DETERMINER. When you read the sentence, what part of speech did you first assign to old? Typically, this word is an ADJECTIVE — abbreviated as J — which is a class of words that modify nouns. Similarly, man is usually a noun. The resulting sequence of tags is D J N D N. But this is a mistaken “garden path” interpretation, which ends up leading nowhere. It is unlikely that a determiner would directly follow a noun,1 and particularly unlikely that the entire sentence would lack a verb. The only possible verb in the sentence is the word man, which can refer to the act of maintain- ing and piloting something — often boats. But if man is tagged as a verb, then old is seated between a determiner and a verb, and must be a noun. And indeed, adjectives can often have a second interpretation as nouns when used in this way (e.g., the young, the restless). This reasoning, in which the labeling decisions are intertwined, cannot be applied in a setting where each tag is produced by an independent classification decision. 

We hence use the POS tags for structure prediction

## Sequence labeling as structure prediction 
*The entire sequence of tags as a label itself*

For a sequence of words $$ \underline{w}_{1:M} = (w_1, w_2, \ldots, w_M) $$ there is a set of possible taggings $$ \Upsilon ({w}_{1:M}) = \Upsilon^M \ {}$$ where $$ \Upsilon $$ refers to the set of individual tags and $$ \Upsilon^M $$ refers to the set of tag sequences of length M. 

Hence the sequence labelling problem can be treated as a classification problem in the label space $$ \Upsilon(w_{1:M}) $$ 
 
 $$ \hat{y}_{1:M} = argmax_{y_{1:M \in \Upsilon(w_{1:M})}} \theta \cdot f(w_{1:M}, y_{1:M}) $$ 

Where $${y}_{1:M}$$ is a sequence of M tags,




## References

* [Dr. Jacob Eisenstein's Notes](https://github.com/jacobeisenstein/gt-nlp-class/blob/master/notes/eisenstein-nlp-notes.pdf)