---
layout: post
comments: true
title:  Detecting edges in RGB images
excerpt: "I will provide a brief review of the paper in this post and compare it to the state-of-the-art methods of today."
date:   2020-11-09 22:00:00
---

Recently I came across the edge detection algorithm by Chaudhuri and Dutta, as I wanted to develop a deeper understading how edge detection works in theory and practice, especially in RGB color space. Their proposed algorithm uses some techniques which are also in use in today's state-of-the-art methods. I will provide a brief review of the paper in this post and compare it to the state-of-the-art methods of today. My very unoptimized Python version of this algorithm is available in [GitHub].

## Overview

Edge detection is an extremely important function in computer vision, as it reduces the amount of data which has to be processed, while preserving useful structural information about object boundaries. About 90% of the edge information of an RGB image is available in the corresponding grayscale image, but the remaining 10% can be important. The paper proposed an edge detection method which consists of four parts; smoothing, directional color difference calculation, thresholding and thinning [1].

I chose this image of a beautiful red Tesla Model S as the object on which I applied the edge detection algorithm. Now let's see step by step how the algorithm works.

![alt Original image of Tesla Model S](https://github.com/jpuum/jpuum.github.io/tree/master/images/t_models.jpg "Tesla Model S")

## Adaptive median filter

Median filters are popular as their noise reduction capabilities for certain types of noise are great. A traditional median filter moves a window over an image and computes the output pixel as the median of the gray values within the window. One problem with traditional median filter is that it doesn't do a great job at preserving detail of an image while smoothing the non-impulse noise, an adaptive median filter seeks to fix this problem. Intuitively, a window is moved over an image, checks are made if the current pixel is noise or if it should be kept as it is. Pseudocode of the adaptive median filter is available in the paper [1]. 

This is what the Tesla looks like after adaptive median filtering, visually not much different from the original in our case.

[Median image here]

## Directional masks

Directional color difference calculation, or applying directional masks is a critical part of the proposed algorithm. Its purpose is to point out areas where abrupt changes of RGB values occur, because that's where the edges are! In general, edges can exist in four directions (0 = horizontal, 45 = ascending diagonal, 90 = vertical, and 135 = descending diagonal), thus we need four masks. The paper proposes the following four masks to be used [1].

[Masks here]

Before applying the masks, pixels are transformed to single valued attributes, using weighted addition of RGB components.

Pixel(i,j) = 2 * red(i,j) + 3 * green(i,j) + 4 * blue(i,j)

To apply the masks, the paper proposes a mathematical model to calculate directional color differences. However, I noticed much better results with convolution. In convolution we flip the masks horizontally and vertically, after which the input window is multiplied elementwise with a mask (not traditional matrix product), and the resulting elements are summed up. Similarly, regardless which method we are using, we move a window (in the paper 3x3) over the image one pixel at a time from top to bottom and left to right. Masks are applied at every window over the image, four times as we have four masks. Consequently, we get four values as a result, of which we choose the biggest one, as the paper suggests. This calculated value is the new pixel value at that spot.

This is what we have after applying the directional masks

[Dirmasks image here]

## Thresholding

Thresholding is mathematically a very simple, but critical operation to get a good edge map. Thresholding in our case, we define a threshold, and say every pixel which is smaller than the threshold becomes white, and every other pixel becomes black. The paper suggests the threshold to be defines as T = 1.2t, where t is the average value of maximum color difference or our convolution map. Thresholding gives us already a pretty decent looking edge map!

[Thresholding image here]

## Thinning

At this point, we already have detected edges in the image, but they are quite thick. Thinning will thin existing edges, the thickest edges will become thinner and the thinnest edges and dots will disappear. In other words, it keeps the most important edges and gets rid of the most spurious edges. Consequently, we will have a quite nice edge map, which is visually appealing! Let's take a look at the result.

[Thinned image here]

## Comparison and conclusion

# References

[1] Dutta, Soumya & Chaudhuri, Bidyut. (2009). A Color Edge Detection Algorithm in RGB Color Space. ARTCom 2009 - International Conference on Advances in Recent Technologies in Communication and Computing. 10.1109/ARTCom.2009.72. 