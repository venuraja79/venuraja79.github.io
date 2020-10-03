---
layout: single
title:  "Building Topic models for Text"
date:   2020-10-04 09:01:10 +0530
categories: jekyll update
---

## Text Text everywhere.
With the rise of social media, short messaging platforms such as twitter, technology discussion forums such as
stackoverflow, hacker news and reddit see continuous activity. As a result, lot of text gets generated and this 
momentum will continue in future not just in English language but also in lot of other regional, 
low resource languages. Also, these social features are slowly seeping into the enterprise products and
helps in producing better collaborate between teams.

Measuring the effectiveness and understanding what gets discussed in these platforms are important for 
leaders in making decisions. Topic modeling provides a rudimentary tool to get a quick bird's eye view 
of the most important topics discussed in the channels.

This article focuses on topic modeling, all channels are not created equal. There are channels 
for question-answering, messaging for communication and discussions. Each channel has its own nuances, however
topic modeling is a good first step for all of them.

## Topic modeling
It is a unsupervised learning technique. Given a corpus of text, the algorithm tries to find the most important
topics (words) from it. There are different approaches to it, the following are covered in this article.

1. Latent Dirichlet Allocation (LDA) and its semi-supervised flavour Guided LDA
2. Non-negative Matrix Factorization (NMF)
3. Clustering / Embedding based methods

### LDA 
Typical technique well supported in most of the contexts is this LDA algorithm. This 
[article](https://www.freecodecamp.org/news/how-we-changed-unsupervised-lda-to-semi-supervised-guidedlda-e36a95f3a164/)
explains it in an intuitive way compared to most of the others that are too high on math.

In short, a document (any text message) is considered as a bag of words. Initially, the words in the 
document are initialized randomly against the topics (number of topics is a hyperparameter). Using an 
iterative algorithm, the conditional probability of a topic given a word in a document p(z|w,D) is 
maximized until convergence.

The most important words for every topic (based on word counts) are produced by the algorithm. Most of the
times,the top 5 words are a very good representative of the topic.

Evaluating the optimal number of topics is a bit tricky and mostly done using coherence scores.

The trained model can be used to produce the topic for new documents / text messages. 
Being a probabilistic method, this algorithm provides a probability score (sums to 1) for every document-topic mapping

### Guided LDA
It is just the semi-supervised flavor of LDA where the initialization of the words with respect to topics
can be done at a higher probablity if the words belonging to the topics are known apriori. This is
definitely a powerful way to code rules into model optimization procedure. This produces
better topics than random initialization of vanilla LDA in the scenarios that we tested. 

### NMF
Given a document-word matrix (M), the method factors them into two matrices W X H. The W matrix provides
the document-topic relationship and the H matrix provides topic-words mapping.

M -> d x w (document x words)
W -> d x z (document x topics)
H -> z x w (topics x words)

The documents are tokenized first using a TF-IDF tokenizer first to produce the Document-Term matrix which
is then used for factorization. A very good implementation is [here](https://github.com/derekgreene/topic-model-tutorial/blob/master/2%20-%20NMF%20Topic%20Models.ipynb)

For smaller texts, this method seems to produce better results compared to the LDA method. 
(not sure if it's proven)

### BERT Embedding
With transfer learning and well documented advantages of BERT models in literature, it is 
unavoidable to apply them for topic models. The LDA / NMF methods are bag of word approaches and don't
consider text semantics. 

A simple method is to generate embedding vectors for every document and perform a k-means clustering.
Then the resulting cluster messages are used to get the top words for every topic.

[Sentence-transformers](https://github.com/UKPLab/sentence-transformers) architecture converts a text message
into a 768d embedding which when used on short messages produced meaningful clusters. 

Finding optimal number of clusters is a challenge, using elbow method with wss (within-cluster sum of square).

## Learning
Given a corpus of text, which method to try?
How to evaluate the topic models?

These two questions are very important and will be convered in the next article.




