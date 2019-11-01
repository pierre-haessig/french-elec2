# Pie vs Dots: exploring Cleveland dot plot to show power system data.

The last weeks, I've read [William S. Cleveland](https://en.wikipedia.org/wiki/William_S._Cleveland) book “[The Elements of Graphing Data](https://www.goodreads.com/book/show/18619.Elements_of_Graphing_Data)”,
because it's known as a classical work on  data visualization.
Of course, on some aspects, the book show its age (first published in 1985), for example
in the seemingly exceptional use of color on graphs.
Still, there are many interesting ideas.
Some  proposed tools have become rather common, like [loess curves](https://en.wikipedia.org/wiki/Local_regression). Others, like the many charts he propose to compare data distributions,
are not so widespread.

One of the proposed tool I wanted to try is the *(Cleveland) dot plot*,
advertised as a replacement of pie charts and (stacked) bar charts, but with a greater visualization power.
Also, he conducted scientific experiments to assess that superiority.

I’ve explored the visualization power of dot plots using the French electricity data from [RTE éCO₂mix](https://www.rte-france.com/fr/eco2mix/eco2mix) (RTE is the operator of the French transmission grid).
I've aggregated the hourly data to get yearly statistics similar to RTE’s [yearly electrical statistical report](https://bilan-electrique-2018.rte-france.com/) (« bilan électrique »).

## The case for dot plot

Such energy data is typically represented with pie charts to show the share of each category of power plants. This is RTE’s pie chart for 2018:

![pie chart for the energy share of each plant type in 2018](img/RTE_2018_energy_pie-chart.png)

However, Cleveland claims that the dot plot alternative enables **more efficient reading** of single point values
and also **easier comparison** of different points together. Here is the same data shown with a *simple dot plot*:

![](dotplots/Dotplot_Powersys_2018_int_colored.png)

Plant types are colored by three general categories: fossile (gas, oil and coal), renewable (hydro, wind, solar and bioenergy) and nuclear. Subtotals for each are included. Gray points are either not a production (load, export. or pumped hydro) or cannot be categorized (imports). Imports, which were only 1 TWh in 2018, are clipped  off the scale to keep a good spread of all the other points (I hesitated cutting oil as well).

Compared to the pie chart, we have the benefit of being able to read directly the absolute value rather than just the shares.
However, how to read the shares?

This is where the *log₂ scale*, also promoted by Cleveland, comes into play. It serves two goals. First, like any log scale, it avoids the unreadable clustering of point around zero when having varying orders of magnitude. However, Cleveland specifically advocates log₂ rather than the more common log₁₀ when the difference in orders of magnitude is small (1 to 500 TWh here) because it would yield a too small number of tick marks (1, 10, 100, 1000 here) and also because log₂ enables comparison or ratios :

* a distance of 1 in log₂ scale is is 50% ratio
* a distance of 2 in log₂ scale is is 25% ratio
* …

Still, I guess I'm not the only to be unfamiliar with this scale, so I made myself a small conversion table:

| Δlog | ratio a/b (%)     | ratio b/a |
|------|-------------------|-----------|
| 0.5  | 71% (~2/3 to 3/4) | 1.4       |
| 1    | 50% (1/2)         | 2         |
| 1.5  | 35% (~1/3)        | 2.8       |
| 2    | 25% (1/4)         | 4         |
| 3    | 12%               | 8         |
| 4    |  6%               | 16        |
| 5    |  3%               | 32        |
| 6    |  1.5%             | 64        |

As an example, wind power (~28 TWh) is at distance 2 in log scale of the renewable total, so it is about 25%. Hydro is distant by less than 1, so ~60%, while Solar and Bioenergies are about 3.5 so ~8% each.
Of course, log scale blurs the precise value of large share. In particular,  nuclear (distant by 0.5 to the total generation) can be read to be somewhere between 65% and 80% of the total, while the exact share is 71.2%. On the pie charts, it is visibly slightly less than 3/4 of the disc, but Cleveland warns us than angles 90°, 180° and 270° are special easy-to-read anchor values where all other angles are in fact difficult to read. For example, how would I estimate the share of solar in the pie chart without the 1.9% annotation? On the log₂ scaled dot plot, only a little bit of grid line counting is necessary to estimate the distance between solar and total generation to be ~5.5, so indeed a about 2% (and with the help of the conversion table…).

## The bar chart alternative?

Along with pie chart, the other classical competitor to dot plot is the bar chart.
It's actually a stronger competitor since it avoids the pitfall of the poorly readable angles of the pie chart.
I (with much help from Cleveland) see three arguments for favoring dots over bars.

The weakest one may be that dots create *less visual clutter*. However, I see a counter argument that bars are *more familiar* to most viewers, so if it was only for this, I may still prefer using bars.

The second argument is that the *length of the bars would be meaningless*. This argument only applies when there is no absolute meaning for the common “root” of the bars. This would be the case here with the log scale. It would also be the case with a linear scale if for some reason the zero is not included.

The third argument is an extension of the first one on clarity in the case several data points for each category must be compared. With bars there are two options:
* stacking bars side by side yields poor readability
* stacking bars on top of each other (if the addition makes sense like votes in an election) makes a loss of the common ground except for the bottom most bar (of left most bar when using the horizontal layout like here)

This yields to the case where dot plots shine most: multyway dot plots

## Multiway dot plots

The compactness of the “dot” plotting symbol (regardless of the actual shape : disc, square, triangle) compare to bars allows superposing several data points for each category.

Cleveland presents multiway dots plots mostly by stacking horizontally several simple dot plots. However, now that digital media allows high quality colorful graphics, I think that superposition on a common plot is better in many cases.

For the electricity data, I plot, again for each plant category:
* the *maximum* power over the year (GW): red triangles
* the *average* power over the year (GW): blue discs

The maximum power is interesting as a proxy to the power capacity.
The average power is simply the previously shown yearly energy production data, divided by the duration of the year.
The benefit of using the average power is that it can be superimposed
on the plot with the power capacity since it has the same unit.
Also, the ratio of the two is the *capacity factor* of the plant category, which is a third interesting information.

Since it is recommended to *sort the categories*, this yields two possible plots: one sorted by the maximum powers,
the second sorted by the average (i.e. the energy share).

![](dotplots/Dotplot_Powersys_2018_bymax.png)

![](dotplots/Dotplot_Powersys_2018_byavg.png)

### Adding some quantiles

Since I felt the superposition of the max and average data was leaving space, I packed 4 more numbers for each category by adding gray lines showing the 90% and 50% range of the power distribtutions over the year.

However, I faced one issue with the quantiles: some plant categories are off for a significant fraction of the time:
* Solar: 52% (that is at night)
* Coal: 29% in 2018
* Import: 96% (meaning that the French grid was a net-importing electricity from its neighbors only  4% of the year in 2018)

To avoid having several quantiles clustered at zero, I chose to use compute them only for the on times. However, to warn the viewer, I drawn those special quantiles in light red rather than gray.
Spending a bit more time, it would be possible to stack on the right a second dot plot showing just the off times to make this more understandable.

## Animated dot plots

### Gif animation

### Interactive
Interactive with [Altair](https://altair-viz.github.io/) (based on [Vega/Vega-Lite](https://vega.github.io/))

Some other blog article on dot plots:

* “Cleveland (Dot Plot) Rocks! (with Tableau)”
https://spencerbaucke.com/2019/09/12/cleveland-dot-plot-rocks/
* UC Business Analytics R Programming Guide, “Cleveland Dot Plots” page
http://uc-r.github.io/cleveland-dot-plots


Notes:

RTE data differs

Link to notebook
