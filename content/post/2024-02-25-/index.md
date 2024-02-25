---
title: Some useful functions for Art II
author: DWD
date: '2024-02-25'
categories:
  - R art
---

```r
thetas = seq(0,1,length=1000)*48*pi
par(pty='s',ann=F)
plot(cos(thetas),sin(23*thetas/24),typ='l',axes=FALSE,mai=0*c(1,1,1,1))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="672" />

