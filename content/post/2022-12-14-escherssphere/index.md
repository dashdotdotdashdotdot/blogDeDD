---
title: "Escher's Sphere"
subtitle: "Finding its Formulas"
author: "Douglas Dwyer"
date: '2022-12-14'
output:
  tufte::tufte_html: default
  tufte::tufte_book:
    citation_package: natbib
    latex_engine: xelatex
  tufte::tufte_handout:
    citation_package: natbib
    latex_engine: xelatex
bibliography: skeleton.bib
link-citations: yes


---

# Introduction

<div class="figure">

<img src="{{< blogdown/postref >}}index_files/figure-html/fig-margin1-1.png" alt="Escher's Sphere" width="480" />
<p class="caption">
Figure 1: Escher’s Sphere
</p>

</div>

Escher created three dimensional spaces that never existed with mathematical precision. He also was very good at conveying a sense of depth on a flat surface. All without the aid of a computer.

About this sphere (made in 1958) he wrote: “Curved ribbons or strips are suited to suggest a three dimensionality… A system of four theoretically never ending ribbons running over the surface of the sphere linking at the poles. Both poles are visible, one externally and one internally through the interspace between the two ribbons.”[^1]

In this note, we reverse his math to determine what he actually did, but we will use a computer.

Escher rotated the sphere forward by 45 degrees, and he used orthographic projection. In order for the ribbons to be never ending one will need a function that maps “latitude” to “longitude” such that a latitude of +/- 90 degrees goes to a longitude of +/- infinity. See note on the side for how we are using these terms and the function that we use.

Note: we are using *latitude* (lat) and *longitude* (long) in more or less the same way they are used in geography. A negative *longitude* goes to the west, and a *longitude* of `\(X + \lambda 360\)` is the same as `\(X\)` whenever `\(\lambda\)` is an integer. The formula that we use to create the ribbon is:

$$long = \alpha + \beta \times log\bigg(\frac{1}{ (lat + 90)/180}-1 \bigg) $$
which is an inverse logistic function. Escher used something that was somewhat different as will be shown.

The first step is to bring the image of Escher’s Sphere into the “plot device” and to “locate” a few key points using the locator function on the *XY plane* on which the image is drawn.[^2] Points 1 and 2 are the *North* and *South* pole respectively. The third point is a tangent point on the west of the sphere, and the fourth point is where one of the strips intersects with the center of the globe. These points are labeled in the figure to the right. We now know these points in the *XY plane* after the sphere has been rotated. We can use this information to determine how the sphere has been rotated and recover the latitude and longitude of points three and four as well as the radius of the sphere (We already know the latitude and longitude of points 1 and 2 as they are the north and south poles.).

<div class="figure">

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" alt="Locating four points on the sphere" width="336" />
<p class="caption">
Figure 2: Locating four points on the sphere
</p>

</div>

We can guess that he rotated the sphere forward by 45 degrees and we will make the radius a bit less than 1 (0.96), which we will allow us to match the poles of the sphere.[^3] These two parameters serve as a reasonable initial guess. The poles of the sphere (the green triangle and blue circle) line up well with the figure.

<div class="figure">

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" alt="Verrifying the sphere is pitched forward by 45 degrees" width="336" />
<p class="caption">
Figure 3: Verrifying the sphere is pitched forward by 45 degrees
</p>

</div>

We now have a reasonable starting point. We want to find the latitude and longitude of points 3 and 4. In order to do so we define a loss function which takes as inputs the radius of the sphere and “roll” of the sphere and the latitude and longitude of points 3 and 4 and computes the location of points 1-4 in the *XY plane*. We can refer to the points computed this way as “modeled.” We can compare them with the corresponding points as measured from Escher’s image and compute the sum of the square of the distance between each point – the *sum of the errors squared*. We treat the pitch of the sphere as known to be 45 degrees forward. We then choose the input parameters to minimize the sum of the loss function. Below is the R code that uses our functions lnl_XYZ_v2(), which maps latitude and longitude to XYZ space and rotateT(), which rotates points in XYZ space. We also use the R function optim().

``` r
#I treat pitch as known to be -45 degrees
pitch = -45*pi/180 

#latitude and longitude of the 
#North and South Poles
poles = rbind( c(90,0),c(-90,0))

#measured points in XY plane
northPole   = c(-0.01126253,  0.6899800)
southPole   = c(0.04068136,  -0.6605611)
westEquator = c(-0.95923848,  0.1229259)
center      = c(0.01470942,   0.07098196)

measuredPoints = rbind(northPole,southPole,
                       westEquator,center)

loss= function(parms) {
  radius1 = parms[1]
  roll = parms[2]
  testPoints_Lat = c(poles[,1],parms[3],parms[5])
  testPoints_Long = c(poles[,2],parms[4],0)
  modeledPoints = rotateT(lnl_XYZ_v2(
                  testPoints_Lat,testPoints_Long,r=radius1),
                  c(pitch,roll,0))   
  loss = (as.vector(modeledPoints[,1:2] 
                    - measuredPoints)^2)
  return(10^6*(sum(loss)))
}

sol3= optim(c(0.96,-1,0,-90,25),loss,control = list( maxit=1000))
t(sol3$par)
```

    ##           [,1]        [,2]     [,3]      [,4]     [,5]
    ## [1,] 0.9610612 -0.01167783 5.261112 -95.05542 49.23198

So we solved for the five parameters that minimized the loss function. We can then plot the figure to determine if it worked. It seems to have worked well. We plot the four points as well as the lines of latitude that correspond with points 3 and points 4. Finally, we plot *Null Island* and the equator (See Figure “Finding the Sphere”). One thing that is revealed is that the 45 degree forward rotation of the sphere yields that the south pole and *Null Island* coincide in the *projective plane*. [^4] The third point is at 5 degrees of latitude and -95 degrees of longitude, which is about where the Galapagos Islands are. The fourth point is at 49 degrees of latitude and 0 degrees of longitude which is about where Southampton in the UK is located.

<div class="figure">

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" alt="Verrifying the solution" width="336" />
<p class="caption">
Figure 4: Verrifying the solution
</p>

</div>

The next step is to solve for the spirals. We will start by solving for `\(\alpha\)` and `\(\beta\)` in the formula below that approximates the edge of the ribbon that intersects with point 4.

`$$long = \alpha + \beta \times log\bigg(\frac{1}{ (lat+90)/180}-1 \bigg)$$`
Treating `\(\beta\)` as given, the value of `\(\alpha\)` that ensures the spiral intersects with the fourth point (i.e., Southampton in the UK) is

`$$\alpha = \beta \times log\bigg(\frac{1}{ (49+90)/180}-1 \bigg)$$`
, where 49 degrees is the latitude of the fourth point.

<div class="figure">

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" alt="Finding the spiral" width="432" />
<p class="caption">
Figure 5: Finding the spiral
</p>

</div>

Once we have this spiral, we can experiment with different values of `\(\beta\)`. The larger the value the more curvature. I choose the value of -95 to match where the spiral first intersects with -90 degrees of longitude. As it doesn’t match everywhere, this finding demonstrates that Escher used some other function.

We can draw the four ribbons by using multiple spirals defined by the set of `\(\alpha\)` and `\(\gamma\)`:

$$\alpha =  log\bigg(\frac{1}{ (49+90)/180}-1 \bigg) + \gamma $$
such that
$$ \gamma \in {[-45,0], [45,90], [135,180], [225,270]}$$
Below we present the side by side comparison of the two figures. Escher’s original is on the left, and the computer generated version (CGI) is the one on the right. This implementation successful conveys the never ending set of four ribbons going around the globe. Nevertheless, the Escher version conveys a better sense of depth. Escher conveyed the depth by giving thickness to the ribbon. He also used different shadings for the inside and outside of the sphere. The outer surface gets lighter as you move from right to left and the inner surface get lighter as you move from right to left, suggesting the light is from the left. Finally, he indicates the surface of the sphere using a grid.

    ##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
    ## -89.0000 -44.5000   0.0000  -0.1716  44.5000  88.2320

<div class="figure">

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-6-1.png" alt="Original and the CGI version" width="816" />
<p class="caption">
Figure 6: Original and the CGI version
</p>

</div>

We solved for the orientation of Escher’s sphere and the formulas for the four spirals that never end as they approach the poles. Improving the sense of depth in the CGI version relative to Escher’s original remains to be demonstrated.

[^1]: *Escher on Escher: Exploring the Infinite*, 1989, Abrams

[^2]: locator is a function in the jpeg R package that enables you to click on a point in a figure and recover the XY coordinates of that point.

[^3]: We use a function that rotates points in *XYZ space*, where *XY* is the *projective plane* and *Z* is *depth*. We will use the term *pitch* to refer to rotating the Z axis towards the X axis, *roll* for rotating the X axis towards the Y axis and *yaw* for rotating the Y axis towards the Z axis.

[^4]: Off the coast of Africa, the location where the degrees of latitude and the degrees of longitude are both 0 is referred to as *Null Island*.
