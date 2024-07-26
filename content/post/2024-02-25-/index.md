---
title: Some useful functions for art
author: DWD
date: '2024-02-25'
categories:
  - R art
---

This post is to keep track of some functions of mine that I find useful in making mathematical art.


# Application of a tri-cube weight function

The tricubic weight function is:

$$ y = (1-|x|^3)^3$$

for x in [-1,1]

So when x is close to 0 it will be 1 and when x is close to 1 or negative it is zero.   It assume has a derivative of 0 at when x is 1, -1 or 0 which makes it asethically appealing relative to say a triangle function. Our application takes as additional input a scaler, knot, and a two element vectoring called streetching that are used to center and streetching the inputed x vector before applying the tricubic weight function.  We also return 0 whenever the transformed x is outside the range [-1,1].

This funcction is useful for making a local smooth adjustment in the context of an annimation.



``` r
triCubeDWD = function(x,knot=0,streeching=c(1,1)){
  y_tmp = (x-knot)
  y = pmin( pmax( as.numeric(y_tmp<=0)*y_tmp*streeching[1] + as.numeric(y_tmp>0)*y_tmp*streeching[2] ,-1), 1)
  return((1-abs(y)^3)^3)
}

x = seq(-4,4,length=1000)

plot(x,triCubeDWD(x),typ="l",col='darkblue',lwd=2)
lines(x,triCubeDWD(x,streeching = c(.3,.3)),col='darkred',lwd=2)
lines(x,triCubeDWD(x,streeching = c(3,3)),col='gold',lwd=2)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="672" />

``` r
plot(x,triCubeDWD(x),typ="l",col='darkblue',lwd=2)
lines(x,triCubeDWD(x,knot= - 0.5),col='darkred',lwd=2)
lines(x,triCubeDWD(x,knot = .5),col='gold',lwd=2)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-2.png" width="672" />

``` r
plot(x,triCubeDWD(x),typ="l",col='darkblue',lwd=2)
lines(x,triCubeDWD(x,streeching = c(0.5, 2)),col='darkred',lwd=2)
lines(x,triCubeDWD(x,streeching = c(2,0.5)),col='gold',lwd=2)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-3.png" width="672" />



# Harmonica
I call this one _harmonica_ as it is based on a harmonagraph.

``` r
harmonica = function(NNN=1000,waves=23, ratio=24/23) {
thetas = seq(0,1,length=NNN)*2*pi*waves
return(cbind(cos(thetas),sin(ratio*thetas)))
}
par(pty='s',ann=F,mai=0*c(1,1,1,1), mfrow=c(2,2))
plot(harmonica(ratio=1,waves=.95),typ='l',axes=FALSE)
plot(harmonica(ratio=4/3,waves = 2.95),typ='l',axes=FALSE)
plot(harmonica(ratio=8/7,waves = 6.95),typ='l',axes=FALSE)
plot(harmonica(waves=22.95),typ='l',axes=FALSE)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="672" />

## in python

``` python
import numpy as np
import matplotlib.pyplot as plt

def harmonica(NNN=1000, waves=23, ratio=24/23):
    thetas = np.linspace(0, 1, num=NNN) * 2 * np.pi * waves
    return np.column_stack((np.cos(thetas), np.sin(ratio * thetas)))

fig, axs = plt.subplots(2, 2)

for ax, ratio, waves in zip(axs.flatten(), [1, 4/3, 8/7, 24/23], [0.95, 2.95, 6.95, 22.95]):
    harmonics = harmonica(ratio=ratio, waves=waves)
    ax.plot(harmonics[:, 0], harmonics[:, 1], color='black', linewidth=0.5)
    ax.set_xticks([])
    ax.set_yticks([])
    ax.set_aspect('equal')
    ax.axis('off')  # Turn off the axes and bounding box
```

```
## [<matplotlib.lines.Line2D object at 0x129db2e10>]
## []
## []
## (-1.0999991692927558, 1.0999999604425121, -1.0999981210196357, 1.0999997033188356)
## [<matplotlib.lines.Line2D object at 0x129e864d0>]
## []
## []
## (-1.0999997923231684, 1.099999990110627, -1.0999357707940272, 1.0999334852952614)
## [<matplotlib.lines.Line2D object at 0x129e87290>]
## []
## []
## (-1.099991225665797, 1.0999995821745618, -1.099998840017093, 1.0999998168447835)
## [<matplotlib.lines.Line2D object at 0x10ddecb90>]
## []
## []
## (-1.1, 1.1, -1.0999998977880034, 1.0999993526573957)
```

``` python
plt.tight_layout()
plt.savefig("harmonica_plots.png", dpi=300)
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="672" />

# Traveled

This function computes the distance traveled along a curve as a percentage of total distance traveled, which can be used to create a new curve where the points are evenly spaced.  Works for a curve that is either in 2D or 3D space. One can make an evenly spaced vector by using linear interpolation for a lookup table is combination of distance traveled and the x (or y vector) and one "looks up" a set of numbers that are evenly spaced on the unit interval.  In the pictures below the "dots" cluster in the corner in the graph on the right as they are not evenly spaced.

``` r
traveled <- function(xy) {
  NN <- nrow(xy)
  if (ncol(xy) == 2) {
    distanceBetween <- c(0, sqrt(rowSums((xy[1:(NN - 1), 1:2] - xy[2:(NN), 1:2])^2)))
  } else {
    distanceBetween <- c(0, sqrt(rowSums((xy[1:(NN - 1), 1:3] - xy[2:(NN), 1:3])^2)))
  }
  traveled <- cumsum(distanceBetween) / sum(distanceBetween)
  return(traveled)
}

# Example:
NNN0 <- 500
xy0 <- harmonica(NNN = NNN0, ratio = 4 / 3, waves = 2.95)
travel <- traveled(xy0)
xy <- cbind(
  approx(travel, xy0[, 1], seq(0, 1, length = NNN0), rule = 2)[[2]],
  approx(travel, xy0[, 2], seq(0, 1, length = NNN0), rule = 2)[[2]]
)

par(pty = "s", ann = F, mai = 0 * c(1, 1, 1, 1), mfrow = c(1, 2))
plot(xy, typ = "l", axes = F)
points(xy, pch = 20, col = rgb(1, 0, 0, .5))

plot(xy0, typ = "l", axes = F)
points(xy0, pch = 20, col = rgb(1, 0, 0, .5))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-3.png" width="672" />


# dougsSign

This function conveniently transforms a sin curve to a given top and bottom and controls where the sin curve starts zero and the number of waves.  


``` r
dougsSign <- function(topBottom, NNN, waves = 2 * pi, start0 = 0) {
  thetas <- seq(0, 1, length = NNN) * waves
  wave <- (topBottom[1] - topBottom[2]) * ((sin(thetas + start0) + 1) / 2) + topBottom[2]
  return(cbind(thetas, wave))
}

dY <- 1 / 4
par(pty = "s", ann = F)

plot(NULL, xlim = c(0, 2 * pi), ylim = c(-1, 1), axes = F)

start <- 32
stop <- 18
for (i in seq(start, stop, -2)) {
  xy <- dougsSign((start - i) * dY / 2 + c(-1, -1 + dY), 1000, waves = i * pi)
  lines(2 * xy[, 1] / i, xy[, 2])
}
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

# polly

This function I call polly as I have cousin with that name. It creates a regular polygon using polar coordinates with a default radius of 1.  If starts is set to 0 (or pi/2) is starts at c(0,1) (or c(1,0)), and it moves clockwise.

``` r
polly = function(sides=4,start=0,r=1){
  thetas=seq(0,1,length=sides+1)*2*pi
  res=r*cbind(sin(start+thetas),cos(start+thetas))
  return(res)}
```



``` r
par(pty='s',ann=F,mai=0*c(1,1,1,1),mfrow=c(1,2))

p1=polly()
p2=polly(start=pi/6)
p3=polly(start=pi/3)
    
plot(p1,t="l",axes=F)
lines(p2)
lines(p3)

p1 = polly(12)
plot(NULL, xlim = c(-1,1),ylim = c(-1,1), axes=F)
text(p1[,1],p1[,2],1:12)
for (i in 1:35) {
  lines(rbind(p1[(i %% 12) + 1,],p1[(i+5) %% 12 + 1,]))  
}
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-1.png" width="672" />
#bigger stars

``` r
par(pty='s',ann=F,mai=0*c(1,1,1,1),mfrow=c(1,2))

p1 = polly(11)

plot(NULL, xlim = c(-1,1),ylim = c(-1,1), axes=F)
text(p1[,1],p1[,2],1:11)
for (i in 1:35) {
  lines(rbind(p1[(i %% 11) + 1,],p1[(i+4) %% 11 + 1,]))  
}

p1 = polly(7)

plot(NULL, xlim = c(-1,1),ylim = c(-1,1), axes=F)
text(p1[,1],p1[,2],1:7)
for (i in 1:14) {
  lines(rbind(p1[(i %% 7) + 1,],p1[(i+2) %% 7 + 1,]))  
}
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-8-1.png" width="672" />

# Whiteside Circle

The function is named in honor of Forbes Whiteside who taught art at Oberlin College.  He used to say tha the way an artist drew a perfect circle was to lightly draw an imperfect circle and then draw another correcting the mistakes of the first one and repeat and the collection of light circles would become a dark perfect circle reinforced by the mistakes or something like that.


``` r
whiteside = function(rho = .9,
                     sigmae = .02,
                     theEnd = 100,
                     width = 1,
                     white = rgb(1, 1, 1, .15),
                     white2 = rgb(1, 1, 1, 1),
                     scale = 1,
                     scaleY = 1,
                     rTheta = 0,
                     shiftV = c(0, 0),
                     fill = F) {
                                start = 0
                                end = theEnd * 64.1
                                e = rnorm(end)
                                mean = 1
                                a = mean + e * sqrt(sigmae ^ 2 / (1 - rho ^ 2))
                                for (i in 2:length(e)) {
                                  a[i] = mean * (1 - rho) + rho * a[i - 1] + sigmae * e[i]
                                }
                                sqrt(sigmae ^ 2 / (1 - rho ^ 2))
                     if (fill == T) {
                       thetas = 2 * pi * seq(0, 1, .01)
                       circle = cbind(scale * sin(thetas), scale * scaleY * cos(thetas))
                       circle = rotate(circle, rTheta)
                       circle = shift(circle, shiftV)
                       polygon(circle, col = white2, border = NA)
                     }

                                
                                
            thetas = (1 / 64.1) * seq(1:end) * 2 * pi
            for (j in 1:theEnd) {
                start = (j - 1) * 65 + 1
                end = start + 65
                circle = cbind(a[start:end] * scale * cos(thetas[start:end]), a[start:end] *
                     scale * scaleY * sin(thetas[start:end]))
                circle = rotate(circle, rTheta)
                circle = shift(circle, shiftV)
                lines(circle,
                typ = "l",
                col = white,
                lwd = width)
                }
}

rotate=function (xy,theta,about=c(0,0)) {
  rows=nrow(xy)
  center=t(matrix(rep(about,rows),2,rows))

  rotate=rbind(c(cos(theta),sin(theta)),c(-sin(theta),cos(theta)))
  rxy =(xy-center) %*% rotate +center

  return(rxy)}
shift=function(xy,shifted)
{
  rows=nrow(xy)
  xynew=xy+t(matrix(rep(shifted,rows),2,rows))
  return(xynew)}

par(pty="s",ann=FALSE,mfrow=c(1,1),bg="grey70",mai=.1*c(1,1,1,1))

 plot(NULL, xlim = c(-1.2, 1.2), ylim = c(-1.2, 1.2),axes=FALSE)
 whiteside(
   rho = .98,
   sigmae = .015,
   theEnd = 500,
   white = rgb(1, 1, 1, .2),
   white2 = rgb(1, 1, 1, 1),
   width = 1,
   fill = F
 )
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-1.png" width="672" />


