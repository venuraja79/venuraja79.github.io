---
layout: single
title:  "Explaining Loss Functions - Part 1"
date:   2020-06-25 17:15:20 +0530
categories: jekyll update
---

## Loss Functions
A loss function lies at the heart of most of the Machine Learning (ML) algorithms. For a newbie, 
the loss functions are a bit daunting. I struggled initially to come to terms with it. 
In most articles, the mathematics behind is explained but not the intuition. 
This is an attempt to make loss functions approachable. I really hope this helps a ML newbie.

Let's understand loss: It is just the difference between the predicted value by the model and 
the actual value. The lesser the difference (loss), the better the model. That's all there is to it. 
The model actually learns by reducing the loss and this is called training.

There are different ML tasks such as classification, regression etc., The way we measure the 'difference'
varies for each task. That is why each task has its own loss function. 

Let's start with **Cross Entropy** and **Mean Squared Error**, used in classification and regression respectively.

### Cross Entropy 
Let's consider a binary classification problem (2 classes, cat vs dog), the actual labels being 
0 for cat and 1 for dog. Now the model is trained to predict a probability between 0 and 1. If the model 
produces a higher probability (let's say 0.9) for dog and a lesser probability for cat (let's say 0.1) 
the loss is lower. 

On the other hand if the model produces 0.9 for cat, the loss should be higher. Of course, a perfect model 
will produce 0 for cat and 1 for dog. Any function that captures this characteristic can be considered 
as a loss function for classification.

Now a bit of mathematics! Cross entropy is calculated by the equation - 
<br>
<math xmlns="http://www.w3.org/1998/Math/MathML" display="block">
  <mo>&#x2212;<!-- − --></mo>
  <mrow class="MJX-TeXAtom-ORD">
    <mo stretchy="false">(</mo>
    <mi>y</mi>
    <mi>log</mi>
    <mo>&#x2061;<!-- ⁡ --></mo>
    <mo stretchy="false">(</mo>
    <mi>p</mi>
    <mo stretchy="false">)</mo>
    <mo>+</mo>
    <mo stretchy="false">(</mo>
    <mn>1</mn>
    <mo>&#x2212;<!-- − --></mo>
    <mi>y</mi>
    <mo stretchy="false">)</mo>
    <mi>log</mi>
    <mo>&#x2061;<!-- ⁡ --></mo>
    <mo stretchy="false">(</mo>
    <mn>1</mn>
    <mo>&#x2212;<!-- − --></mo>
    <mi>p</mi>
    <mo stretchy="false">)</mo>
    <mo stretchy="false">)</mo>
  </mrow>
</math>
<br>
Here:
**p** --> predicted probability<br>
**y** --> actual label(s)

<br>
Here is the python implementation using numpy.
{% highlight python %}
import numpy as np
def CalculateEntropyLoss(p, y):
    loss = -(y * np.log(p) + (1-y) * np.log(1-p) )
    return loss
{% endhighlight %}

Now, Let's unpack this equation with an example. For a dog, the actual label is 1.
If the predicted probability is 0.1, the resultant loss from the above equation is 2.3. 
If the probability is 0.9, the loss is 0.1. You can see that this function fits our bill, the loss reduces as the 
predicted value is closer to actual value. You can try on your own. It works the other way as well.

You can learn more [**here**](https://ml-cheatsheet.readthedocs.io/en/latest/loss_functions.html)

The example code is [**here**](https://colab.research.google.com/drive/1FElLRoubzuejXwFnudayfE-LL_hgjZD0?usp=sharing)

One more thing to note: Because of the log, the losses actually increase at log scale when 
the predictions move towards the wrong side. This can be seen in the plot below. 

<div class="imgcap" align="center">
<img src="/images/loss/cross_entropy.png" height="75%" width="75%">
<p>penalized more closer to 0 - from mlcheatsheet site</p>
</div>

### Mean Squared Error (MSE)
A regression task predicts a continuous variable. A squared error is a commonly used loss function in this task.
As the name suggests, it is a mean of the squared error. The error is the difference between the actual value 
and the prediction, the error being squared to remove the -ve sign and then averaged for MSE.

Another reason for squaring the loss is that it produces a continuous convex function where Gradient Descent can
be applied for learning. More on this in another article.

It is comparatively simpler to understand than Cross Entropy. Here is the python implementation.
<br>

{% highlight python %}
import numpy as np
def CalculateRMSE(p, y):
  size = y.size
  loss = np.sum((p - y)**2) / size
  return loss
{% endhighlight %}

You might have noticed L2 norm or Euclidean norm mentioned in papers, it just means RMSE.

### Mean Absolute Error (MAE)
It's similar to MSE, instead of squaring the error, the absolute value of the error is considered. 
This is also called L1 or Manhattan Norm.

Another thing to note is that the RMSE is highly sensitive to outlier values as you would have guessed.
This is because the square term. If the dataset has more outliers, prefer MAE. Otherwise, MSE works great!





[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
