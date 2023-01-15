---
title: Transforming Positive to Negative
author: R package build
date: '2023-01-14'
categories:
  - R art
tags:
  - ConnectTheDots
  - Tesselation
slug: transforming-positive-to-negative
---

In this note, we show how to create the "negative space" around a silhouette starting with a silhouette that is represented with a set of points in 2D space.  With the combination of the silhouette and its negative space we could make a variety of tessellations. We will focus on transforming the positive space into the negative space and thereby make the negative the positive in a tessellation context (see middle row of the last figure). We first need to choose a silhouette. We will start with a shape that is simple, organic and easily recognizable.  Further, in order to get a pleasing transformation, we will want a silhouette that allows for a "smooth mapping" of points on one side of the silhouette to points on the other.  We will use a silhouette that has been derived from a hand-cut silhouette.


```
## 
## Attaching package: 'ArtMathdeDD'
```

```
## The following object is masked _by_ '.GlobalEnv':
## 
##     makeDrawingsData
```

```
## 
## Attaching package: 'aPhotoDeDD'
```

```
## The following objects are masked from 'package:ArtMathdeDD':
## 
##     adjPicApp, adjustPicture, getPixels, makeTessalation
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="50%" /><img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-2.png" width="50%" />
The next step is to transform the drawings so the points start just above the x axis and move in a counter clockwise direction.  See figure below in which we number every 30th point.

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="50%" />

We can then construct the negative space of silhouette in few steps.  We find the "top" , "bottom", "left most", "right most" points of the drawing.  From these points we can compute the four corners of the rectangle that encloses the drawing.  The outline of this rectangle is drawn in dark grey in the figure below.  We define the left-hand negative shape as the polygon formed by the left-hand corners of the enclosing rectangle and the left-hand side of the drawing (the points between the top and the bottom on the left side of the drawing). Likewise for the right-hand negative shape. We then shift the left-hand negative shape to the right by the width of the enclosing rectangle.  The negative space silhouette is the union of the right-hand negative shape and the shifted left-hand negative shape.  Care needs to be taken to order the points correctly. We now have a pairs of shapes that 'tessellates' in the sense that one could fill an infinitely large table with cut outs of both the silhouette and negative space silhouette as seen in the figure below.

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="50%" />

We wish to smoothly transform the black shape into the white shape.  We can do this be taking the weighted average of the two shapes, but before we do that we need a "one-to-one" mapping of the points in the black shape and the points in the white shape. Further, in order for transformation to be pleasing, the points need to align well.  We accomplish this by creating new versions of each shape in which each point on corresponding side of each shapes aligns horizontally (has the same coordinate see figure).  We accomplish this by creating 4 functions that take a value for the Y coordinate and returns the X coordinate for the left and right side of each figure.  We accomplish this by ordering the points correctly and using linear interpolation.  We than use this function to map 1,000 evenly spaced values of Y to each side of each shape to build up new versions of the figures.

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="50%" />

The top row of the figure below is the "positive shape" repeated 10 times and each time shift to the right by the width of the enclosing rectangle.  The bottom row of the figure is the same thing except is uses the "negative shape".  The positive shape emerges from between the negative shapes.  In the middle row, the "positive shape" is gradually transformed into the "negative shape" and as a result the "positive shape" emerges from the grey background as we move to the right.  It aligns with the top row on the left and the bottom row on the right.

MC Escher would often take a geometric tessellation and gradually transform it into a pair of organic forms.  What I am doing here is inspired by that work, but I am not aware of him (or others) doing exactly the same -- transforming an organic shape into the negative of the shape. I have not seen it.

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

