---
layout: post
title:  "Introduction to SageMaker"
description: Machine Learning (ML) with AWS SageMaker
date:   2020-05-20
---
If you are more of a watcher than a reader, then visit my youtube [video](https://www.youtube.com/watch?v=0UdJkjsqD0E&t=) which has what I have written here.

I anticipate FooBar Youtube videos every Tuesday and Thursday. Until today, I only heard about SageMaker and had a vague concept of what it is. However, from FooBar's Youtube channel (recommended by friend Paul Kukiel) engaging video, I now know what SageMaker is. It's AWS's fully managed machine learning service. It gives you a platform with different functionalities intergrated within it.

First question that came to my mind was, how is SageMaker different from traditional ML development. AWS [page](https://aws.amazon.com/sagemaker/) explains it beautifully and in simple terms that in traditional ML dvelopment you need to install various tools and workflows which is cubersome, time-consuming and there are more chance of making mistakes. The process is costly, involves a lot of repititions and manually handled tasks.

SageMaker, makes the entire process extremely easy to use for all levels, from novice to experienced individuals. It has all the tools/components(discussed below) and workflows embedded in it. It does all the complex things for us so all we need to focus on is to develop high quality models. How awesome is that!!

## Tools and Components:

ML has the below lifecycle (what I like to call it) process:

![](/assets/images/2020-05-20/sagemaker1.png)

## Label your data. 
This is done by service in SageMaker called Amazon SageMaker Ground Truth.

## Building, Training & Tuning and Deploying & Managing the model 
SageMaker Studio is used as an IDE. If you are from .NET background you can say it's "Microsoft Visual Studio" but for ML.

### SageMaker AutoPilot
If you are just beginning with ML, SageMaker has this cool feature which automatically builds and trains your model. Very useful if you want to get your hands dirty first time.

### SageMaker Experiments
For intermediate to advanced ML developers who know what they are doing. ```Experiments``` allow them to capture, organize and search every step.

### SageMaker Notebooks
This is on what the model runs on. You can call it a virtual server or in AWS terms EC2.

### SageMaker Debugger
For intermediate to advanced ML developers - If your model isn't running / behaving as expected, you can use the the debugger to find out why.

### Automatic Tuning
For intermediate to advanced ML developers - Used for tuning the model using one-click hyperparameter optimization.

### SageMaker Model Monitor
This is used for automatically detecting concept drift. What concept drift means is that the underlying distribution of data is changing unexpectedly. Consequently, the result/predictions become less accurate.

### SageMaker Neo
Enables you to train your model once and deploy it anywhere.

### Augmented AI
It used to add human review of model predictions.


I hope you have some idea about SageMaker and what it can do now. If you have any questions, feel free to contact me through my [website](https://roarcoder.dev). I'll be more than happy to answer any questions you have.