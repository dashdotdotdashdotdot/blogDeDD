---
title: How to Draw with Math
author: DWD
date: '2023-01-06'
slug: how-to-draw-with-math
categories:
  - R art
tags:
  - ConnectTheDots
---


I have always been fascinated with line drawings and silhouettes. How could something two dimensional convey a sense of emotion and personality? Also, a line drawing can convey the illusion of depth. Further, a two dimensional drawing can be mapped to a 3D surface and when successful it can convey a sense of the shape of the surface -- the third dimension.

In this note, I show an approach I developed to create a "connect the dots" representation of a simple line drawing or a simple silhouette. By connect the dots, I am thinking of an exercise one may find in a child's coloring book where a set of dots are numbered. When the child draws line segments connecting each consecutive point a recognizable drawing emerges. I will shows applications of such a representation.

# Alternative Approaches

There are many YouTube videos on how to create a silhouette with a pen tool in software such as Adobe Illustrator, Adobe Photoshop, Graphic Concepts.  There are illustrators that can obtain excellent results with a pen tool (cf. the work of Malika Favre). The illustrations can be saved as a .svg file, in which the silhouette that was created using the pen tool is represented as a set of Bezier curves.  One could presumably write a program to extract the parameters of each path from the .svg file and then transform these mathematically. I prefer my approach for two reasons. I did spend sometime making silhouettes using a pen tool in photoshop. While I could get satisfactory results with patience, I find it much more satisfying to drawing with an Apple Pencil, and the result has more of a _hand drawn_ feel that I like.  The second reason is that the resulting "connect the dots" representation is a much more intuitive representation that is easy to work with mathematically.

# The Approach

The first step is to start with an image that one could trace with either a graphite pencil using tracing paper or an apple pencil and ipad. I typically use the latter. I will start with a picture of a teapot that I took (below). The teapot is a non trivial example of the problem because it "has a hole" and the outer silhouette is not a "convex" set.


```
## Linking to ImageMagick 6.9.12.3
## Enabled features: cairo, fontconfig, freetype, heic, lcms, pango, raw, rsvg, webp
## Disabled features: fftw, ghostscript, x11
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

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="200" />

Below is the drawing that I created with an Apple pencil and an Ipad. I have experimented with a variety of approaches to automate the conversion of a silhouette using commercial software to varying levels of success. I prefer to trace by hand. Tracing takes a few minutes and allows for artistic control of the end result.

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="200" />

When we bring it in as an image, we have a pixel representation where some of the pixels are dark colors and some of the pixels are light colors (the colors are represented with an RGB color model). To use a statistical approach, we want to transform the pixels representations into "observations." One can think of an observations as a pixel in the image that was marked by the pencil. We seek a table of pixels with two columns -- the X and Y coordinates of the marked pixels. The function as.raster converts the image into an array of columns and rows where each cell is an RGB color. From this representation, we convert it into a data table (actually a data frame) in which one column is the row number of the pixel (i.e., the x coordinate) and another column is the column number of the pixel (i.e., the y coordinate) and the third column is the color of the pixel as represented using the RGB colorspace. From the RGB values, we select the pixels where the resulting average of each color is less than 100. These are the dark pixels as we are working with a high contrast image with little color. We now have a representation of the image as 1,471 points in two dimensional space (left-hand panel in the figure below). As the zoom (center panel of figure below) reveals, this is not a mathematically convenient form to work with as there are many dots due to the thickness of the line (the thickness is typically about 2 points). At this stage, if one "connects the dots" one actually recovers something resembling the "convex hull" of the teapot (right-hand panel in figure below), which is not what we want.  This happens because when we converted the observations from an array to a table the observations became sorted by row of the pixel first and then the column of the pixel second. 

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="672" />

One approach to reduce the number of points is to use a statistical learning technique called _K-Means Clustering_. What this approach does is find a specified number of clusters and assign each observation to a cluster and computes the distance of each observation from its cluster. It then chooses the clusters to minimize the sum of the distance of each observations from its cluster. We can extract the clusters from the procedure and reduce 1,471 observations into 300 points (for example). The left-hand panel of the figure below shows the new set of dots. The rightpanel shows a "connect the dots" version of these points which results in a jarring configuration of jagged lines.  The clusters are not yet in the correct order for a connect the dots representation.

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="672" />

There are many ways to order 300 dots. A mathematical description of what makes one the best is not readily obvious. Nevertheless, one can mathematically describe what is a bad ordering and what would be a better ordering. Also, for some drawings, one will want multiple lines -- the virtual equivalent to where the artist picked up the pencil to make a new line. My approach starts by choosing the "next point" such that it is closest to the previous point. I then choose the starting point such that of all the possible starting points (there are 300) the resulting path is the shortest. Finally, I will choose the numbers of times that I break the line into multiple lines. I find that this approach is close to "what I drew" across a wide variety of drawings. The left-hand panel in the figure below shows the result of from choosing a poor starting point (the red dot). When the line returns to the red dot it makes a long jump to the next points which is the "hole" formed by the inside of the handle. 

The right-hand image in the figure below is the result from choosing the starting point to minimize the resulting distance traveled given the constraint of always moving to the next closest point. This results in a small jump between the outer shape and the inner shape that is consistent with the size of the handle.


<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

We next break the lines where the distance between two consecutive points is the greatest. This results in the final representation: two sets of points, one for each line, where the points are ordered to give a pleasing line drawing (left-hand panel of figure below). Using a two polygons, one that is black for the outer shape and one that is white for the "hole", we can create a silhouette of the teapot.
Typically, the outer shape will be the line that has the most points and if we color this polygon first in black and the remaining polygon(s) on top of it in white we will get the desired results (right-hand panel of figure below).   

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-6-1.png" width="672" />

We can now draw the silhouette ontop of the photograph to see if the approach worked.  We then make a storyboard for an animation using this approach.  Once we have the two sets of lines we can smooth them (by fitting cubic splines through them), distort them and rotate them to create something resembling a bird. Instead of using black and white, we can use the color of the teapot and the color of the shadow in the photograph.  We can reverse the distortion and then gradually fade between the silhouette and the actual photograph. Such an animation is intended to be mesmerizing as the viewer starts seeing a child like drawing being made in 2D space that then smoothly into the everyday 3D world of a teapot on a deck.  Then animation then reverses itself and goes on forever.

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-1.png" width="672" />
<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-8-1.png" width="672" />


<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-1.png" width="672" />
