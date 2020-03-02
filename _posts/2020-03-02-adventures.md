---
layout: post
title: Tracking Fly Behavior with a Camera
---

### *Tracking output of a large Drosophila erecta collective in the presence of food dishes*
![](https://i.imgur.com/cyywHzZ.gif)

### *Background*
One of the most important behavioral readouts in the fruit fly *Drosophila* is free walking behavior. Although fruit flies spend much of their lives flying, reducing the dimensionality of the problem to planar motion can help isolate motivated behaviors such as the pursuit of food, pursuit of a mate or aversion of a predator.  The Ruta Lab has extensive interest in automated tracking of the planar trajectories in *Drosophila*. Machine learning for object detection and localization is an indispensable tool for our lab.

### *2 Fly Courtship*
The most common assay in our lab is a 2-fly courtship assay with 1 male and 1 female fly. It has been observed that the male will perform a ritual including tapping the female with his front leg, pursuing her at a close distance, and singing a species-specific song by vibrating his wing. The variability in this highly-stereotyped behavior gives important insights into evolutionary pressures of different *Drosophila* species, as well as insight into the structure and function of the neural circuits driving courtship.

The lab is interested in high-throughput trials to test many courting pairs, and human-scoring (watching the video and making note of how much time the male spends courting) is a tedious process.  One of the first tools I built in the lab was an automated tracker in Python and OpenCV to perform simple threshold-based contour detection to locate the flies in the frame. The process is as follows:

* Binarize the image using a darkness threshold, flies should now be black blobs and everything else will be white
* Use OpenCV contour detector to find ALL contours, many of which will not be flies (shadows, fly feces, etc)
* Generate metrics for each contour: size, length-width ratio, average color, etc and vectorize
* Create a hand labelled dataset of fly/not-fly dependent on the contour properties
* Build a small binary classifier in Keras to determine fly/not-fly, excluding non-fly contours

Once true-fly contours were found, interesting features like heading angle and distance between the flies could be extracted. One of the most clear-readouts of courtship, and therefore one of the best targets in automating the courtship-scoring process, is a sharp transition in interfly distance, where the male transitions from "not caring" to suddenly pursuing the female at a very close distance. The tracker was used to generate the following video, illustrating the sharp decrease in fly-distance.

![](https://imgur.com/2qoobLf.gif)

This method was used to automate courtship scoring for multiple lab members interested in fly courthsip.

### *Issues with Contour Detector for Tracking*
The presence of shadows, shifts and occlusions was a constant source of error for this assay. Even with an emphasis on repeatable experimental setup, small shifts could generate erroneous reads that made this method suboptimal. The soution was to train a custo YOLO network to localize the flies, particularly in difficult situations such as occlustions and harsh lighting that would make a pixel-intensity-threshold tracker impossible to use. Shown here is an example of the custom YOLO network detecting flies in suboptimal lighting.

![](https://lh6.googleusercontent.com/EdhNKC0syCEfRoxV1WJnS6JwYMAQUgyeWGdVImwRywlC51_2HsG-hVZ3x-E-filCtUhoBPSJ1UucZZxBZxpN4pdRM-V9_DPBHWYEnLwlLg=s320)

This trained network was also a boon to some of the larger-format tracking the lab was interested in.

### *Tracking Large Collectives of Animals over Long Periods of Time*
