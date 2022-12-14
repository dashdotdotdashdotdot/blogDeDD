---
title: Algorithimic Figure Ground Reversal
author: DD
date: '2022-12-14'
slug: AlgoGroundReversal
categories:
  - R art
tags: []
---

Artists have long been fascinated with the concepts of figure and ground reversals.  In this note, we show how to execute such techniques with mathematical operations on photographs. We will start with two images.  The image on the left is one of a chess board.  It is a photograph that I have taken. The image on the right is  "Silhouet van Madame Eugène de Block-Looz," which is a "snipping" done by Charles Auguste Fraikin in  1852 and is available on website of the Rijks Museum. For both images I  have increased the contrast to convey the concepts more clearly.  We wish to place the Silhouette (the Figure) on the chess board (the Ground) and be able to simultaneously convey both images. If we just placed one on top of the other, the silhouette would be lost in the black squares. We will use a "figure ground reversal" to convey both images.


<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="100%" />

Most photo editing software (e.g., Photoshop, Procreate, and ImageMagick) have ways to combine images using different blending modes or “operators”.  Also one can take the negative of an image.  For this note, we will use two operators: lighten and darken.  What these operators do is take two images and go pixel by pixel and select the lighter or the darker pixel. One application is to take the same “moonscape” picture of the moon at two different times using a tripod and then combine them using the "lightening operator" resulting in one picture with two moons.  Another application is to take two pictures on a sunny day with a shadow at different times using a tripod and combine the result with a "darkening operator" resulting in one picture with two shadows.

The negative operator takes the inverse of an image. One application of this operation is to take a film negative and make a positive image out of it.  Here we will convey a silhouette on a chess board by letting it be dark on the white squares and switching the darks to lights on the white squares.  An elegant way to do this is to use repeated application of the above operators.  

First, combine chess board and silhouette using the darkening operator which results in the silhouette appearing as dark on the light squares, but the dark squares are unchanged.  We will call the result _picDark_.



```r
picDark = image_composite(board1,drawing1,operator = "darken")
picDark
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="50%" />

Next combine the silhouette with the chessboard using the lightening operator.  Now the negative space of the silhouette appears as white on the dark squares. We can call the result _picLight_.  


```r
picLight = image_composite(board1,drawing1,operator = "Lighten")
picLight
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="50%" />


Now take the negative of _picLight_ which makes the black squares white and the silhouette white on the black squares.  This is effectively reversing the ground and figure.


```r
image_negate(picLight)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="50%" />

Now if we combine the _picDark_ and the negative of _picLight_ using the lightening operator the white squares contain the silhouette and the dark squares are the negative of the silhouette — the desired result:



```r
image_composite(picDark,image_negate(picLight),"lighten")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="50%" />
