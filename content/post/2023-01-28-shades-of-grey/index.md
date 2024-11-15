---
title: Shades of Grey
author: DWD
date: '2023-01-28'
slug: shades-of-grey
categories:
  - R art
tags:
  - aPhotoDeDD
---

This notebook demonstrates some of the basic photo manipulation that I like that can be done with the package aPhotoDeDD ((current location is (https://github.com/dashdotdotdashdotdot/aPhotoDeDD)).  One image caps all the pixels whose that are lighter than a grey value to the grey value.  The other sets all the pixels that are less than the grey texture.  The iconic image of Che Guevera is in the public domain.



```r
#rm(list=ls())
library(aPhotoDeDD)
library(magick)
```

```
## Linking to ImageMagick 6.9.12.3
## Enabled features: cairo, fontconfig, freetype, heic, lcms, pango, raw, rsvg, webp
## Disabled features: fftw, ghostscript, x11
```

```r
pixels <- 400  
pic1 <- demo_dd("Che.jpg")
pic1 <- adjustPicture(pic1,bsh=c(100,0,100),pixels=pixels,return=0)   #returns an image with a maximum dimension as Pixels
pic1
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="151" />


```r
border <- 1.1
withinBorder <- .1 # border between pictures
center0 <- pixels * border / 2 # center of picture
wxh <- image_info(pic1)[2:3]

canvasPixels <- as.numeric(border * max(wxh[2], (2 + withinBorder) * wxh[1]))
leftBorder <- (canvasPixels - (2 + withinBorder) * wxh[1]) / 2
topBorder <- 0.75 * (canvasPixels - wxh[2]) / 2

centerBorder <- canvasPixels - 2 * leftBorder - 2 * wxh[1]

canvasSize <- paste0(canvasPixels, "x", canvasPixels)
canvas0 <- image_blank(wxh[1], wxh[2], col = "grey55")
canvasF <- image_blank(canvasPixels, canvasPixels, col = "grey55")

picDark <- image_composite(canvas0, pic1, operator = "darken")
picLight <- image_composite(canvas0, pic1, operator = "lighten")

offSet1 <- paste("+", leftBorder, "+", topBorder)
bigPicDark <- image_composite(canvasF, picDark, operator = "atop", offset = offSet1)

bigPicLight <- image_flop(image_composite(canvasF, image_flop(picLight), operator = "atop", offset = offSet1))

bigPicLight2 <- image_composite(canvasF, picLight, operator = "atop", offset = offSet1)

offSet1 <- paste("+", leftBorder + wxh[1] + centerBorder, "+", topBorder)

bigPicBoth <- image_composite(bigPicDark, picLight, operator = "atop", offset = offSet1)
bigPicBoth
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="348" />

We can also do a little animation:


```r
bigPicBoth2 <- image_composite(bigPicLight2, picDark, operator = "atop", offset = offSet1)

alldone <- image_morph(c(
  canvasF,
  bigPicDark, bigPicDark,
  bigPicLight, bigPicLight,
  bigPicBoth, bigPicBoth,
  bigPicBoth2, bigPicBoth2,
  bigPicBoth, bigPicBoth,
  bigPicLight, bigPicLight,
  bigPicDark, bigPicDark,
  canvasF, canvasF
), frames = 20)
image_animate(alldone)
```

![](index_files/figure-html/unnamed-chunk-3-1.gif)<!-- -->

One can output a gif if desired.

```r
outputGif <- FALSE
if (outputGif == TRUE) {
  image_write_gif(alldone, "Che.gif")
}
```
