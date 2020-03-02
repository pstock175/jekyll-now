---
layout: post
title: Unsupervised Clustering of Male Courtship Behaviors
---

![](https://imgur.com/HglaOay.gif)
Computationally-corrected video of male and female perspective on courtship. Made using custom software.

### *Background*
One common model for animal behavior is treating it as a "language" or series of stereotyped behavioral syllables. Isolating and understanding how these syllables are mediated and displayed in response to stimuli is an area of ongoing research in the animal neuroscience and *Drosophila* neuroscience communities. 

This post is a brief overview of an exploratory data tool that I built to try and help the lab identify stereotyped behavioral syllables in the courting male fruit fly. It is adapted off of a method from the lab of the Datta Lab at Harvard Medical School (http://datta.hms.harvard.edu/research/behavioral-analysis/) for similar analysis in mice, and included collaboration with the Datta Lab.

Although this data exploration produced interesting insights, it did not yield reliable enough data to be used in automating lab analysis, and needs further work to be fully adapted for fruit flies.

### *Data Collection*
The input data was high-bandwidth video of a courting pair of *Drosophila Melanogaster* wild-type flies. The chamber width was 15mm, which is smaller than traditional courtship arenas to create images where the flies occupy a larger ratio of the camera's receptive field. The resolution of the crop around the male is 244 x 244 pixels.

### *Data Cleaning*
The data was then cleaned by subtracting the background, normalizing pixel intensity, and rotating to an egocentric alignment of the male. An animation of both the male and female egocentric alighments is at the head of this post. The consistent alignment allows the images to have highly similar pixels at the center, normally the abdomen of the male fly, with increasing variation at greater distance from the center. This means that pixel changes caused by motions of the legs, wings, and female mate will constitute a reduced feature-space for the images.

The input data is 244 x 244 pixels, so we can consider our unmodified feature vector to be 59,536-dimensional. This can be greatly reduced though, because many of the pixels are providing redundant informtion. For example, male flies extend their wings to 1 of 3 angles. If a feature could signify which one of these 3 positions the wing is in, we don't need all of the 1000s of pixels in the wings to reinforce that. 

A better choice is to use Principal Component Analysis to create a vector that describes the input image with a lower number of varibles than 59,536.

### *PCA*
After running PCA on the images of courting males, we were able to obtain some interesting results on what defines a given frame in our video. A few principal component vectors were arbitrarily selected to show the differnece in importance of various factors.

![](https://imgur.com/4fQ6CVj.png)

As expected, the largest source of variance from the male perspective is whether or not the female mate is directly in front, and this is reflected by the first principal component.  We see that across the first ~10 components, the largest sources of variance are male wings position and the right/left positon of the female mate. By the 50th PC, the source of explained variance is too complicated to relate directly to a fly behavioral analog.

### *Unraveling of Low Dimensional PC Vector*
We can use our learned PCA matrix to modify each frame in our video and reduce the dimensionality to 38, which explains most of the variance in each frame. These unraveled PC vectors (y axis) plotted against time (x axis) show hints of the syllable nature of the male behavior.

![](https://imgur.com/5pCbMx3.png)

### *Segmentation of Syllables*
As can be seen from the above timeseries of PC's there are some PC's active for the duration of the video, creating the long horizontal stripes in the plot. Some of these are to be expected, because of the consistent format of the images, and for example may correspond with the pixels of the centered male. However, the limiting factor in this analysis was the inability to create the PC timeseries with more distinct syllable breaks.

Each syllable should correspond to a repeatable, stereotyped behavior obesrvable to the eye in the courting male fly but this was not always the case. Large changes such as the presence of a female in front of the male could be well-detected in an unsupervised manner, but more nuanced aspects of the behavior such as approach angle and wing extensions were frequently missed. Improving the quality of PC's generated and the algorithm to segment the PC timeseries is an ongoing effort in the lab.

