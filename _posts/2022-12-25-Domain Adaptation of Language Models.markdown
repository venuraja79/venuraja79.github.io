---
layout: single
title:  "Training Language Models for out-of-domain scenarios"
date:   2022-12-20 17:15:20 +0530
categories: jekyll update
---

## Overview
Language Models are the talk of the town. They produce state-of-the-art results in many tasks such as 
semantic search, text classification and summarization when compared with traditional NLP methods. 
However, when presented with same language tasks in a new domain (Engineering, Legal or Biomedical), 
they often come up short and perform less optimal than even traditional techniques. 

We are using a set of techniques to nudge the models to work well in the out of domain scenarios. All the out of domain
scenarios are not same! In some cases, the models can learn the nuances even with a few training examples. In others,
the language model itself have to be augmented. In this paper, we will discuss the techniques and how we leveraged them
in some of our projects to improve the model's performance.

## Language Models
Language models (BERT, GPT) just predict the next word given an initial prompt. They have been trained on large corpus of text 
and have gained intuitive understanding of the language structure, semantics and domain. We almost always download the 
pretrained model checkpoints from [**Hugging Face Hub**](https://huggingface.co) or other open hubs that host the models. 
We can't use these models as is and have to custom-train them. For example, a classification task
requires an architecture change (adding a classification head) and training with a classification dataset. This process is 
called fine-tuning.

## Customizing Language Models
[Transformer](https://arxiv.org/abs/1706.03762) is an [encoder-decoder](https://jalammar.github.io/illustrated-transformer/) architecture.
The tasks such as classification, NER, Question-Answering just depend on the encoder part of the model where as tasks that
generate text (summarization, language translation) requires both encoder and decoder.

Customizing a model can be as simple as training with a few examples on a particular language task. Sometimes, 
it can be a complex process that involves full retraining of the model with large corpus of domain specific text 
and augmenting its dictionary. Here are some of the model customizations that have worked well for us in the projects...

* Fine-tuning on a custom task
* Augment the language model for a new domain
* Semantic search in domain specific data
* Few-shot learning with sentence transformers for classification
* Aligning the models towards human preferences with Reinforcement Learning

### 1. Task based fine-tuning
Many of the projects that we execute involve fine-tuning. We start with a task specific model checkpoint from the hub and
create a baseline on the task specific performance metric (accuracy, F1-score etc.,). For a Question Answering (QA) system, 
we used the SQUAD trained model as our baseline. This model generalizes well, if the customer's dataset is not very domain specific. 
We typically create a pipeline to generate annotated examples (question-answer pairs from user feedback) 
and fine-tune the model with this additional examples.

For text classification and NER, we start with a Language Model (LM) and add a classification head to it before fine-tuning with a
customer specific dataset as the labels are different for every problem. In the projects that we have executed, the time 
spent on collecting good training data provides the most bang for the buck than the architecture choices and hyperparameter tuning.

Some important points to consider -

* In many cases, a few hundred examples for each class can produce good enough models. When there is a change in architecture,
(addition of classification head) under-fitting is a real concern if the data sizes are small.

* Consider using smaller variants of the models such as DistillBERT to reduce both training and inference times without
losing too much accuracy.

* If the domain is entirely different, more training examples are required. We may also need to pretrain the LM itself, which is
explained in the next section.

* The Attention based transformer language models have a 512 token limitation. In most of the real-life projects, the documents
are large and doesn't fit this 512 token limit. We follow a combination of summarization & pooling methods to overcome this.

* Setup the MLOps process so that further training can be automated.

More details on the [training procedure](https://huggingface.co/docs/transformers/training) is here.

### 2. Augment a Language model for a domain

When the problem domain is different, we first continue to pre-train the language model with domain specific text corpus. 
The augmented model is then fine-tuned as usual for specific tasks such as classification. This has produced better 
results ( a few % points gain in performance) for us in both legal and engineering domain problems that we have attempted.

Every generic pretrained LM comes with its own dictionary which is generated by a sub-word tokenization technique. The first 
step in the customization process is to preprocess the domain corpus to generate a domain specific token list (dictionary).
Then the embeddings for the new tokens are learnt by a training procedure using the domain text.

Please refer the [**ULMFit paper**](https://arxiv.org/pdf/1801.06146.pdf) and the [**notebook**](https://github.com/fastai/fastbook/blob/master/10_nlp.ipynb) for more information.

It is also possible to create a domain specific LM from scratch without actually using any pretrained LM at all. But, this
process requires large text corpus and more GPU compute. This [**paper**](https://arxiv.org/abs/2007.15779) has more details
on this.

### 3. Improving Semantic search results

Most of the search platforms such as elasticsearch/Solr are powered by keyword based algorithms such as BM25 by default. 
Semantic search improves search relevance as they use model based embeddings. It works on how similar the documents are in the vector space 
relative to a search query.

We have implemented semantic search powered by model based embeddings to improve the search performance on engineering document repositories.
When the vanilla embeddings from the sentence-transformers (a flavor of BERT model fine-tuned for search) are used, 
the search performance is not optimal. So, we fine-tuned embeddings so that related documents are closer in the 
vector space whereas un-related documents are as far away as possible. 

We annotated a training set (few tens to hundreds of examples - query, related and unrelated triplets) and fine-tuned the model with 
the [**GPL**](https://github.com/UKPLab/gpl) technique. The training data creation can be automated as explained in this paper.

### 4. Few-short learning for classification
With just a few examples, this technique seems to produce pretty good results in classification. The idea is to 
start with an already pretrained sentence transformer model and fine-tune it with very few examples in the domain. The 
[**setfit**](https://github.com/huggingface/setfit) framework provides more details of the training process.

As we already know, Sentence transformers are pretrained to generate embeddings based on document's proximity. If the documents
are far from each other, the embeddings also will be so in the vector space. In a contrastive classification setting, this
property helps to distinguish different classes of documents and seems the magic behind this model that makes it work so well.

This is a recent technique and we plan to apply this in our future projects.

### 5. RLHF - Reinforcement Learning from Human Feedback

There is so much attention recently on [chatGPT](https://openai.com/blog/chatgpt/) model from OpenAI which is fine-tuned
for dialogue. The origins for the techniques used in chatGPT are from this 
interesting paper [Learning to Summarize](https://openai.com/blog/learning-to-summarize-with-human-feedback/) that uses
Reinforcement Learning to align the model towards human preferences.

Language models have their own style that shows up in tasks such as summarization or language translation. A model trained
on arxiv papers will produce text that will look professional and scientific in style. If we want to nudge the models
towards a style that humans prefer, this method seems to be the way to go. We see a lot of potential uses for this 
technique in our summarization work to align the models towards a particular style or domain. 

Compared to other techniques discussed, this is comparatively complex and requires designing a reward function that
outputs a higher reward for the text in the preferred style and less reward otherwise. More details are [here](https://huggingface.co/blog/rlhf)

## Parting thoughts
Most of the time, it is not enough to just integrate the pre-existing models or third party APIs when building AI
capabilities for production use cases. The Machine Learning professionals have the need to custom train the models
for a particular domain. We can't treat these models as black box and have to gain an intuitive understanding of both the
architecture and the neural network training basics such as backpropagation. Actually, this makes the Machine learning journey more
exciting and rewarding.













