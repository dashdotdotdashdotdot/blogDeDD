---
title: Basic Anamorphism
author: Package Build
date: '2024-11-10'
slug: basic-anamorphism
categories: []
tags: []
---
Let a drawing be represented via a set of points on the plane defined by
z = 1, where `\(xy_i\)` is a representative point:

$$xy_i =  \{x_i,y_i,1\} $$ Consider a piece of paper that is rotated
towards the z axis such that the center of the paper is at the point
`$$\{ 0,0,1 \}$$` and the relationship between the y points and the z
points on the paper are given by:

$$ y = \delta (z-1) \; or ; z = (y + \delta)/\delta $$

We wish to solve for a point on the paper whose projection is `\(xy_i\)`. A
representative point on the plane can be expresses as:

$$ XYZ_i = \{ X_i, \delta(Z_i-1),Z_i \}$$

which has the projection:

$$ pXYZ_i = \bigg( \frac{X_i}{Z_i}, \frac{\delta(Z_i-1)}{Z_i} ,1 \bigg)$$
Given `\(\{x_i,y_i\}\)` we wish to solve for XYZ that has that projection,
which implies

$$ y_i = \frac{\delta(Z_i-1)}{Z_i} $$ and

$$x_i = \frac{X_i}{Z_i} $$ First solve for Z_i:

$$Z_i = \frac{\delta}{\delta-y_i} $$ and then `$$Y_i = \delta(Z_i-1)$$`
and `$$X_i = x_iZ_i$$`.


``` r
harmonica = function(NNN=1000,waves=23, ratio=24/23) {
thetas = seq(0,1,length=NNN)*2*pi*waves
return(cbind(cos(thetas),sin(ratio*thetas)))
}

polly = function(sides=4,start=0,r=1,NNN=0){
  thetas=seq(0,1,length=sides+1)*2*pi
  square=r*cbind(sin(start+thetas),cos(start+thetas))
  if (NNN>0) {
    travels = traveled(square)
    square2 =cbind( approx(travels,square[,1],1:NNN/NNN)[[2]], approx(travels,square[,2],1:NNN/NNN)[[2]])
  } else square2 = square 
  return(square2)
  }


anamorphism = function(drawingIn,lambda=.3,delta=.85){
  drawing2 = cbind(lambda*drawingIn,rep(1,nrow(drawingIn)))    
  box0 = polly(4,start=pi/4,r=lambda*sqrt(2))

  Zd = delta/(delta - drawing2[,2])
  Zb = delta/(delta - box0[,2])
  

  drawingNew = cbind(drawing2[,1]*Zd, delta*(Zd-1),Zd)
  boxNew = cbind(box0[,1]*Zb, delta*(Zb-1),Zb)
  
  return(list(drawingNew,boxNew))  
}

par(pty="s",ann=FALSE,mfrow=c(1,1),bg="white",mai=.1*c(1,1,1,1))
drawing = .8*harmonica()
drawingNew = anamorphism(drawing[,1:2],delta=0.25,lambda=.1)

drawing0 = drawingNew[[2]]
rangeX = range(drawing0[,1])
rangeY = range(drawing0[,2])
maxDiff = max(rangeX[2]-rangeX[1],rangeY[2]-rangeY[1])
xlimits = c(mean(rangeX)-0.5*maxDiff,mean(rangeX)+0.5*maxDiff)
ylimits = c(mean(rangeY)-0.5*maxDiff,mean(rangeY)+0.5*maxDiff)

plot(drawingNew[[2]],xlim= xlimits,ylim=ylimits,typ='l',axes=FALSE,col='lightblue',lwd=3)

lines(drawingNew[[1]])
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="672" />

More generally we can solve for the interesection on a ray from the
origin through the point on the drawing and an arbitrary plane.

Any point on a specific plane can be expressed as a linear combination of 2 vectors plus through a starting point:

$$ XYZ_0 + AB[t_1,t_2]^T $$
where `\(AB\)` is a 3x2 matrix of vectors and `\(XYZ_0\)` is a point in `\(R^3\)` expressed as 3x1 vector, and t_1 and t_2 are the "weights" for each vector.


We can define a ray through a point on the picture plane as:

`$$[x_i,y_i,1]^TZ$$`
where [x_i,y_i,1]^T is a 3x1 vector.  We wish to solve for t_1,t_2 and Z such that the two points coincide:

$$[x_i,y_i,1]^TZ =  XYZ_0 + AB[t_1,t_2]^T $$
or 

$$[x_i,y_i,1]^TZ -  AB[t_1,t_2]^T =  XYZ_0  $$
or

$$ABxy[-t_1,-t_2,Z]^T =  XYZ_0  $$
where `\(ABxy\)` is the matrix formed by combining `\(AB\)` with `\([x_i,y_i,1]^T\)`

which can be solved by inverting this matrix:


$$[-t_1,-t_2,Z]^T = \bigg(ABxy\bigg)^{-1} XYZ_0  $$
which allows one to map any point on a picture plane to a point on another plane.


