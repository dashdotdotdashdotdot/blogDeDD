---
title: Some useful functions for art
author: DWD
date: '2024-02-25'
categories:
  - R art
---

I call this one _harmonica_ as it is based on a harmongraph.

```r
thetas = seq(0,1,length=1000)*48*pi
par(pty='s',ann=F,mai=0*c(1,1,1,1))
plot(cos(thetas),sin(23*thetas/24),typ='l',axes=FALSE)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="672" />
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
NNN=500
thetas = seq(0,1,length=NNN)*12*pi
xy0 = 1*cbind(sin(thetas),cos(thetas*5/6) )
travel = traveled(xy0)
xy = cbind(approx(travel,xy0[,1],seq(0,1,length=NNN),rule=2)[[2]],
           approx(travel,xy0[,2],seq(0,1,length=NNN),rule=2)[[2]])

par(pty='s',ann=F,mai=0*c(1,1,1,1),mfrow=c(1,2))
plot(xy,typ='l',axes=F)
points(xy,pch=20,col=rgb(1,0,0,.5))

plot(xy0,typ='l',axes=F)
points(xy0,pch=20,col=rgb(1,0,0,.5))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="672" />


This function conveniently transforms a sin curve to a given top and bottom and control where the sin curve starts zero and the number of waves.  


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

