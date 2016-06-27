---
layout: post
title: Importing Map Shapefiles
---

Importing shapefiles can be useful for building maps, and especially for seeing how variables are expressed spatially. 

Shapefiles for many geographical layers are publicly available. For instance, [Texas Parks and Wildlife](http://tpwd.texas.gov/gis/data) provides many useful shapefiles.

Downloading from this site will give you a .zip file.
IMPORTANT: You must retain the entire contents of this .zip archive! Not just the shapefile itself, as it relies on the other contents.

To import a shapefile:

```
library("maptools")
```

```
zip <- readShapePoly("TexasCenturyClub/Counties/Counties.shp")
```

That's it!
Of course, you'll then want to plot this shapefile in some way.
Let's try plotting the number of birds species that I've seen in each Texas county.

First, we need to define the interval breaks and color palettes for plotting.
Here, we'll define the interval breaks ourselves, and use a pre-made color palette from the 'wesanderson' library, called 'Zissou'.
You can also ask R to determine the interval breaks itself, as commented below.

We have a dataset called 'tcc', which lists how many species I've seen in each county.

Let's merge that with our shapefile. The county names obviously match.

```zip$Species <- tcc$Species```

Set color breaks
Fewer breaks makes color distinction easier. 
Want to be symmetric around 100 for this specific case
Max break point must be ABOVE max observed value

```cats <- c(0,20,50,75,100,125,150,300)```

Zissou color pallete, interpolated to match number of break points

```
colors <- wes_palette("Zissou", length(cats), type="continuous")
brks<-classIntervals(zip$Species, n=length(cats), style="fixed", fixedBreaks=cats)
brks<-brks$brks 
```

Now let's do the actual plotting!
This might take awhile to actually run.

```
plot(zip, col=colors[findInterval(zip$Species, brks, all.inside=TRUE)], axes=F, main="Number of bird species I've seen in each county of TX")
legend("topleft", legend = c(brks), lty=c(1), lwd=20, bty='n', col = colors, cex=0.75, title="Species")
```

Not bad!
And for whatever reason, directly saving the plot as a jpg speeds it up considerably.

```
jpeg(file = "tcc.jpg", width = 1500, height = 1500)
plot(zip, col=colors[findInterval(zip$Species, brks, all.inside=TRUE)], axes=F, main="Number of bird species I've seen in each county of TX")
legend("topleft", legend = c(brks), lty=c(1), lwd=50, bty='n', col = colors, cex=2, title="Species")
dev.off()
```

Here's the final result!

![image](/images/tcc.jpg)

Coming soon, I'm working on making interactive maps, where you can fully explore the map by zooming, clicking on counties for full information, etc.
I'm also working on grabbing the species count data from web scrapes, though that work is further away.