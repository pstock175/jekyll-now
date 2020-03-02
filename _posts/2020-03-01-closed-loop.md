---
layout: post
title: Realtime Olfactory Virtual Reality and Optogenetic Activation System
---

### *Background*
This was a project undertaken to support researchers studying the dual role of dopamine to drive reinforcement learning and behavior motivation in the fruit fly *Drosophila*. The previously-existing system involves a fruit fly tethered to a pin and walking on an air-suspended omni-directional treadmill as shown in the picture here.

![](https://i.imgur.com/eRQJuhL.png)

A great open-source tool called FicTrac exists to capture the motion of the ball, and convert it to a fictitious 2-dimensional planar trajectory for the fly. If the fly is left on the ball with no stimulus, its 2-dimensional path is essentially a forward-biased random walk.

Flies have an innate interest in walking upwind, so a tube providing air is positioned in front of the fly to induce it to walk forward. We use a stepper motor to adjust the angle of the tube, such that if the fly turns 90 degrees, the air now strikes its side. Remember! The fly is not actually walking anywhere, but has been fooled to thinking it is walking on an infinte 2-D plane with a wind source.

Another reason that fruit flies are such an amazing neuroscience research tool is optogenetics. These are controlled mutations which gives researchers the ability to activate or suppress a target neuron by shining red light on the fly. For example, if a fly with neurons that mediate mating behavior targeted encounters red light, their drive to mate can be instantly enhanced, even in the absence of a partner.  Driving behaviors with red light stimulation is a crucial part of this assay.

### *Proportional Gradients*
Fruit flies are attracted to apple cider vinegar (ACV), and adding this odor to the air stream can additionally modify their behavior. The first closed-loop responsive system I built simply read out the y-distance the fly had walked upwind, and adjusted the proportion of ACV in the air stream to be higher. This simulates an odor source and the change in intensity mimics the enhanced sensation as the fly gets closer to the source. This is a simple computation to calculate the setpoint and transmit to the flow controllers that regulate odors, running a simple serial get-and-set program.

![](https://i.imgur.com/I7w6HGo.png)

Flies exhibited a robust response to this gradient, progressively increasing their walking speed as they approached the source. This generated interest in the lab in extending this tool to multiple odors on the fictive 2-D plane.


### *Multiple Odors*
The 1-D single odor gradient was a simple enough assay that only needed access to the realtime y-position variable. But what if the experimenters wanted multiple fictive odor sources? Maybe of different odors? How do they mix? There is only one tube interfacing with the fly, so it was necessary to send the computed setpoints to multiple flow controllers, allow the odor to mix in the tube and be delivered to the fly, and in realtime so that it fools the fly into believing it is a naturalistic experience.

Rather than computing hardware setpoints on the fly (pun!) I designed a 2D odor landscape editor that saves the 3-channel (Odor 1, Odor 2, Air) array of setpoint tuples like a photo. This way, no computation is done during the experiment, rather the x,y position is rounded to the nearest pixel value and a lookup on the array is performed to return the odor setpoint. This enabled complex and dynamically chaning odor environments that would be difficult to code with analytic functions. The editor was built using PyQt and runs on a local socket server that recieves position information from Fictrac, looks up setpoints from the pre-designed 2D landscape, and commands the hardware.

![](https://i.imgur.com/5Ff5OZT.png)

### *Optogentic Reinforcement*
In addition to a responsive odor landscape, my interface allowed the experimenters to define behavior rules to optogenetically reinforce, such as turning, time spent stationary, upwind velocity, etc. This can be used to reinforce non-naturalistic behavior in the flies such as straight-line walking that lasts many minutes.

