---
layout: post
title: "Deep Learning Crash Course"
date: 2017-07-14
author:
 - abbiepopa
---

If you have a twitter feed like mine (i.e., nerdy) you can hardly go a day without seeing some mention of "deep learning." In fact a quick glance at google anayltics 
shows that searches for deep learning have been rising over the past 5 years. I included "linear regression" to have a point of comparison. (You'll note the famous 
"people search for this more when school is in session" trend associated with linear regression.)

![alt text](https://github.com/abbiepopa/blog_post_drafts/blob/master/Screen%20Shot%202017-07-12%20at%2010.28.35%20AM.png?raw=true  "Google Analytics Graph")

So what's all the fuss about? Here, I will offer resources that form a crash course that will be enough to let you play around with deep neural networks. Note, this is not a (apologies) deep
 explanation. For that I highly recommend [Stanford's CS231 class](http://cs231n.stanford.edu/), available online.
 
If you want to skip my commentary see the last section of this post.

# Deep Learning, ANNs, CNNs... what's all the rage? 

To start, some vocabulary. Deep learning refers to a branch of [artificial neural networks](https://ujjwalkarn.me/2016/08/09/quick-intro-neural-networks/), so let's 
start there. Artificial neural networks (ANNs) consist of an input layer that gathers your data into the network, hidden layers that perform transformations on the data, and an
output layer that gives you the predictions.

![alt text](https://ujwlkarn.files.wordpress.com/2016/08/screen-shot-2016-08-09-at-4-19-50-am.png?w=996&h=736 "Graph from the 'artifical neural networks' link")

The magic of ANNs is that they "learn." That is, you don't need to determine the weights between the nodes in advance. The algorithm will compare output from a training set
of data to an independent test set. Then it will [backpropagate](https://en.wikipedia.org/wiki/Backpropagation) to adjust the weights between nodes. It determines the
adjustment algorithmically, most commonly through [gradient descent](https://github.com/mattnedrich/GradientDescentExample). You don't necessarily have to understand gradient
descent to start playing around with ANNs, but it can be nice. Conceptually, in the simplest case you can imagine a bowl, where the lowest point in the bowl represents 
the optimal solution. Gradient descent follows the slopes of the bowl to arrive at that solution. Now keep in mind that the slopes you alogorithm is following may not
be so simple as a bowl, they might be more like terrain. It will follow the slopes to attempt to find the lowest point in the terrain. Though one disadvantage of gradient
descent is it may get "stuck" in a local low point, rather than the true low point.

Deep learning refers to deep neural networks, which are any ANNs that have more than one hidden layer. Having more than one hidden layer allows you to have non-linear transformations
occur between the different features in your dataset.

Most commonly when people refer to deep learning they are referring to [convolutional neural networks](https://ujjwalkarn.me/2016/08/11/intuitive-explanation-convnets/) 
(CNNs). CNNs are important because they learn the features of the data. Let's say, for example, you are trying to classify images as cats or dogs. Without a CNN you may 
decide that the important features of the image are the lengths of the edges as determined by some edge detection algorithm (which you also select). With a CNN, you would
directly feed the images into the network and it might (or might not!) select an edge detection feature to classify the data. 

![alt text](https://ujwlkarn.files.wordpress.com/2016/08/giphy.gif?w=748 "Graph from 'convolutional neural networks' link")

How this feature detection occurs is a full blog post in itself, which is why I link you to [Ujjwal Karn's](https://ujjwalkarn.me/) blog posts. Huge shout-out for that blog
since I'm linking it twice and did not write it!

One thing I want to emphasize about CNNs is they learn their own features! The most common misunderstanding I have encountered teaching CNNs to people in person is a belief
that you have to tell it "I want to use the edge detection filter, the red palatte filte, and the things that are shaped like hedgehogs filter." This is not the case, when
starting from scratch the filters are randomly initialized (ish) and adjusted through backpropagation. One downside of this is there is no guarentee the filters will be 
something that is easy to understand (like the examples I have been giving). 

# Applications

ANNs can be used for just about any prediction you want to make where you have a training set (in other words, it's supervised learning). CNNs particularly have been used
 for image classification. Though they can be used on other high dimensional data, and I am more and more seeing them applied to other applications including natural language
 processing, and training AI to play video games! The reason you might not be using ANNs with your own data is in general to get a high degree of accuracy you need a lot of
 data, whereas regression still works with (relatively) small datasets.

# Play-around Sites

Once you have read those posts (or possibly while you are reading) it is a good idea to get some conceptual understanding of what happens when you tune different parts of
 the network. The [tensorflow playground](http://playground.tensorflow.org) is a great place to see how tuning the many different aspects of a CNN affect it's ability to
 identify different patterns of data.
 
[This demo](https://cs.stanford.edu/people/karpathy/convnetjs/demo/cifar10.html), which came out of the CS231 class mentioned earlier, is also a fantastic resource. It lets
 you visualize what the different features look like.
 
# Playing around with real data.

If you're like me, reading about something is all well and good, but to really understand it you have to do it. When I first started playing with CNNs I used [tflearn](http://tflearn.org/)
 which is a frontend for tensorflow (google's CNN package). Look around on the [tflearn github](https://github.com/tflearn/tflearn) and you'll see examples that go beyond
 the tutorial on the webpage. I started with the [MNIST tuturial](https://github.com/tflearn/tflearn/blob/master/examples/images/convnet_mnist.py). (If you start googling
 around for tutorials you will quickly learn the popularity of MNIST!) I applied what I learned in these tutorials to a kaggle image classification dataset. The particular
 competition I did is no longer active on kaggle (though you can see my teams repo [here](https://github.com/davisincubator/sashimdig) if you like), but there's almost
 always a computer vision competition active on kaggle, so I'm sure you can find something!
 
I found tflearn to be a great starting point for coding CNNs because it is comfortable and easy to read. [Keras](https://keras.io/) is also a great library, and I will mention
 why I'm switching to keras in my advanced topics section.
 
There's also of course the option of writing in tensorflow itself. I've only done this when working with someone else who knows tensorflow well, since you really get more
 in the weeds with no frontend. That said, it will afford you more control to use tensorflow instead of tflearn or keras.

# Advanced topics it's worth knowing exist

I won't go into these in too much detail, but there's a few things worth mentioning for your googling pleasure.

First, often when using CNNs, individuals don't start from a completely naive network. Often pre-trained networks are used. This is why I switched to keras, as it has many
 pretrained networks built in.
 
Second, deep learning is resource intensive and can take a long time to run. As such, many people run it on their graphics cards. Cuda is a tool you can google to do this. 
 It is also useful to get up and running with some cloud service (like AWS) where you can get more horse-power on demand.

# Just the links, please!

I hope you enjoyed this crash course in deep learning (and primarily CNNs)! If you want the links with my commentary, here they are!
* [Stanford's CS231 class](http://cs231n.stanford.edu/)
* [Artificial Neural Networks](https://ujjwalkarn.me/2016/08/09/quick-intro-neural-networks/)
* [Backpropagation](https://en.wikipedia.org/wiki/Backpropagation)
* [Gradient Descent](https://github.com/mattnedrich/GradientDescentExample)
* [Convolutional Neural Networks](https://ujjwalkarn.me/2016/08/11/intuitive-explanation-convnets/) 
* [Tensorflow Playground](http://playground.tensorflow.org)
* [CNN Demo of Feature Detection](https://cs.stanford.edu/people/karpathy/convnetjs/demo/cifar10.html)
* [tflearn](http://tflearn.org/)
* [Keras](https://keras.io/)