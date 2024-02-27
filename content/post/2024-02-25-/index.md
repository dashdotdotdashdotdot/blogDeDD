---
title: Some useful functions for art
author: DWD
date: '2024-02-25'
categories:
  - R art
---

This post is to keep track of some functions of mine that I find useful in making mathematical art.

# Harmonica
I call this one _harmonica_ as it is based on a harmonagraph.

```r
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

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="672" />

# Traveled

This function computes the distance traveled along a curve as a percentage of total distance traveled, which can be used to create a new curve where the points are evenly spaced.  Works for a curve that is either in 2D or 3D space. One can make an evenly spaced vector by using linear interpolation for lookup table is combination of distance traveled and the x (or y vector) and one "looks up" a set of numbers that are evenly spaced on the unit interval.  In the pictures below the "dots" cluster in the corner in the graph on the right as they are not evenly spaced.

```r
traveled = function(xy){
  NN = nrow(xy)
  if (ncol(xy) == 2) {
  distanceBetween = c(0,sqrt( rowSums((xy[1:(NN-1),1:2]-xy[2:(NN),1:2])^2 ))) } else {
    distanceBetween = c(0,sqrt( rowSums((xy[1:(NN-1),1:3]-xy[2:(NN),1:3])^2 ))) }
  traveled = cumsum(distanceBetween)/sum(distanceBetween)
  return(traveled)}

#Example:
NNN0=500
xy0 = harmonica(NNN=NNN0,ratio=4/3,waves=2.95)
travel = traveled(xy0)
xy = cbind(approx(travel,xy0[,1],seq(0,1,length=NNN0),rule=2)[[2]],
           approx(travel,xy0[,2],seq(0,1,length=NNN0),rule=2)[[2]])

par(pty='s',ann=F,mai=0*c(1,1,1,1),mfrow=c(1,2))
plot(xy,typ='l',axes=F)
points(xy,pch=20,col=rgb(1,0,0,.5))

plot(xy0,typ='l',axes=F)
points(xy0,pch=20,col=rgb(1,0,0,.5))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="672" />


# dougsSign

This function conveniently transforms a sin curve to a given top and bottom and controls where the sin curve starts zero and the number of waves.  


```r
dougsSign = function(topBottom,NNN,waves=2*pi,start0=0){
  thetas = seq(0,1,length=NNN)*waves
  wave = (topBottom[1]-topBottom[2]) *( (sin(thetas+start0)+1)/2) + topBottom[2]
  return(cbind(thetas,wave))
}

dY = 1/4
par(pty='s',ann=F)

plot(NULL, xlim=c(0,2*pi),ylim=c(-1,1),axes=F)

start=32
stop = 18
for (i in seq(start,stop,-2)){
  xy = dougsSign((start-i)*dY/2+c(-1,-1+dY),1000,waves=i*pi)
lines(2*xy[,1]/i,xy[,2])
}
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="672" />

# polly

This function I call polly as I have cousin with that name. It creates a regular polygon using polar coordinates with a default radius of 1.  If starts is set to 0 (or pi/2) is starts at c(0,1) (or c(1,0)), and it moves clockwise.

```r
polly = function(sides=4,start=0,r=1){
  thetas=seq(0,1,length=sides+1)*2*pi
  res=r*cbind(sin(start+thetas),cos(start+thetas))
  return(res)}

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

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="672" />


