---
layout: post
title:  "Introduction to AWS Rekognition"
description: Basic understanding of what AWS Rekognition is and what it can do.
date:   2020-05-22
---
Rekognition is an image analysis service from Amazon Web Services (AWS). It's used to give you all kinds of information about the image such as 

*   Labels
*   Face search 
*   Text in image
*   Face deteection and analysis
*   Unsafe image and video detection
*   Celebrity Recognition
*   Real-time video analysis
*   Pathing

Let's understand with an example:

You upload the below image to your application



![](/assets/images/2020-05-22/before-rekognition.png)

After Rekognition processes it, visual representation of the result is the below image.

![](/assets/images/2020-05-22/after-rekognition.png)

As you can see the image has been marked with labels stating what object is and it's **_confidence level_**. Confidence level means how confident Rekognition is about what a particular object and it's represented in percentage `%`.

Hope it makes sense now. If you have any question feel free to contact me :)