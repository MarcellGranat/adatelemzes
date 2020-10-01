Data analysis - ggplot
================
Marcell Granát
2020 10 01

``` r
library(tidyverse)
```

``` r
data(mpg)
```

How many columns we have?

``` r
length(mpg)
```

    [1] 11

``` r
ncol(mpg)
```

    [1] 11

How many observations do we have?

``` r
nrow(mpg)
```

    [1] 234

Let us see the **STR**ucture

``` r
str(mpg)
```

    tibble [234 x 11] (S3: tbl_df/tbl/data.frame)
     $ manufacturer: chr [1:234] "audi" "audi" "audi" "audi" ...
     $ model       : chr [1:234] "a4" "a4" "a4" "a4" ...
     $ displ       : num [1:234] 1.8 1.8 2 2 2.8 2.8 3.1 1.8 1.8 2 ...
     $ year        : int [1:234] 1999 1999 2008 2008 1999 1999 2008 1999 1999 2008 ...
     $ cyl         : int [1:234] 4 4 4 4 6 6 6 4 4 4 ...
     $ trans       : chr [1:234] "auto(l5)" "manual(m5)" "manual(m6)" "auto(av)" ...
     $ drv         : chr [1:234] "f" "f" "f" "f" ...
     $ cty         : int [1:234] 18 21 20 21 16 18 18 18 16 20 ...
     $ hwy         : int [1:234] 29 29 31 30 26 26 27 26 25 28 ...
     $ fl          : chr [1:234] "p" "p" "p" "p" ...
     $ class       : chr [1:234] "compact" "compact" "compact" "compact" ...

## Brief story of ggplot2

  - This package is an implementation of Leland Wilkinson’s **Grammar of
    Graphics** (here comes the gg prefix)
  - Created by Hadley Wickham in 2005
  - There are many packages in order to extend the basic ggplot2:
      - GGally
      - gganimate
      - ggpubr
      - ggthemes
      - etc.

## The structure of ggplot

  - To create a plot with gg, you always need the **ggplot()** function,
    which has ‘data’ and ‘mapping’ argument, and you need a **geom**.
      - **data**: dataset to use for plot (here we use the mpg
        data.frame)
      - **mapping**: list of aesthetic mappings to use for plot, always
        in an **aes()** function (like what variables should be on the x
        axis and what on the y axis)
      - **geom\_**: type of plot

<!-- end list -->

``` r
ggplot(data = mpg, mapping = aes(x = displ , y = hwy))+
  geom_point()
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

Alternativly the **data** & **mapping** can be specified inside the geom
part. It is useful, when you have multiple dataset or geom on one
figure.

``` r
ggplot() + geom_point(data = mpg, 
    mapping = aes(x = displ , y = hwy))
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

Change the color to red (color = “red”) & increase the size (size = 2)

``` r
ggplot(data = mpg, mapping = aes(x = displ , y = hwy))+
  geom_point(color = "red", size = 2)
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

Color everything corresponding to the manufacturer. In this case we need
to add the color arguement inside the aes(), because we use a variable
from the dataset.

``` r
ggplot(data = mpg, mapping = aes(x = displ , y = hwy,
                                 color = manufacturer)) +
  geom_point(size = 2)
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Things you can specify

1.  color
2.  fill
3.  shape
4.  alpha (transparency)

#### Shape and fill

If you want to add a fill and a color argument to a point chart, then
you need to change the shape to one, which has both (Any which has a
blue inner part).

<img src="C:/rproject/adatelemzes/figures/points-symbols.png" width="576" />

``` r
ggplot(data = mpg, mapping = aes(x = displ , y = hwy,
                                 fill = manufacturer))+
  geom_point(size = 2, shape = 21, color = "black")
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

#### Change the transparency

It is useful if you have many points and they cover each other.

``` r
ggplot(data = mpg, mapping = aes(x = displ , y = hwy,
                                 fill = manufacturer))+
  geom_point(size = 6, shape = 21, color = "black", alpha = 0.7)
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

### Import dataset from the OECD webpage

I downloaded a csv file from the OECD webpage. If you open your
downloaded files in your browser, then you can see the source url. I
copy and paste it in to the read\_csv function.

``` r
fertility <- read_csv("https://stats.oecd.org/sdmx-json/data/DP_LIVE/.FERTILITY.../OECD?contentType=csv&detail=code&separator=comma&csv-lang=en")
```

``` r
head(fertility)
```

``` 
# A tibble: 6 x 8
  LOCATION INDICATOR SUBJECT MEASURE   FREQUENCY  TIME Value `Flag Codes`
  <chr>    <chr>     <chr>   <chr>     <chr>     <dbl> <dbl> <lgl>       
1 AUS      FERTILITY TOT     CHD_WOMAN A          1960   3.5 NA          
2 AUS      FERTILITY TOT     CHD_WOMAN A          1961   3.5 NA          
3 AUS      FERTILITY TOT     CHD_WOMAN A          1962   3.4 NA          
4 AUS      FERTILITY TOT     CHD_WOMAN A          1963   3.3 NA          
5 AUS      FERTILITY TOT     CHD_WOMAN A          1964   3.2 NA          
6 AUS      FERTILITY TOT     CHD_WOMAN A          1965   3   NA          
```

We need only the \#1, \#6 & \#7 column, since the others contain
information, which we dont need rigth now.

``` r
fertility<-fertility[,c(1,6,7)]
```

Lets create a figure, but lets add some labs to that.

``` r
ggplot(data = fertility, mapping = aes(x = TIME, y = Value, color = LOCATION)) +
  geom_line() + labs(
    x = "Year", y = "Fertility", color = "Countries", title = "Fertility rates",
    subtitle = "1960 - 2017", tag = "Figure 1",
    caption = "total fertility rate in a specific year is defined as the total number of children \n that would be born to each woman if she were to live to the end of her child-bearing years"
  )
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

### Ggplot themes

With the theme you can change the style of your figures. Here are some
exapmles:

``` r
ggplot(data = fertility, mapping = aes(x = TIME, y = Value, color = LOCATION)) +
  geom_line() + theme_minimal()
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

``` r
ggplot(data = fertility, mapping = aes(x = TIME, y = Value, color = LOCATION)) +
  geom_line() + theme_grey()
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

In other packages you may find a lot more. (And yes, you have to use
install.packages again to download the package before you use it for the
first time)

``` r
library(ggdark)

ggplot(data = fertility, mapping = aes(x = TIME, y = Value, color = LOCATION)) +
  geom_line() + dark_theme_grey()
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-21-1.png)<!-- -->

If you read the The economist newpaper, then you probably will like the
following

``` r
library(ggthemes)

ggplot(data = fertility, mapping = aes(x = TIME, y = Value, color = LOCATION)) +
  geom_line() + theme_economist()
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-22-1.png)<!-- -->

If you want to avoid writing “+ theme\_” all the time, you can set a
theme as defaul with the set\_theme() function.

``` r
set_theme(theme_light()) 
```

\[I use dark theme in my RStudio, so for example I always start coding
with set\_theme(dark\_theme\_grey()) to not hurt my eyes\]

Lets use the filter function to modify the fertility data.frame. With
the following it will contain observation only from Hungary (HUN).

``` r
fertility_hun <- filter(fertility, LOCATION == "HUN")
```

If you want to have more contries in your data.frame, then use the %in%
sign. It means: LOCATION variable is HUN or AUS or AUT.

``` r
fertility_hun_aus_aut <- filter(fertility, LOCATION %in% c("HUN", "AUS", "AUT"))
```

With the facet\_wrap you can present the time-series on different panel.

``` r
ggplot(data = fertility_hun_aus_aut, mapping = aes(x = TIME, y = Value)) + 
  geom_line(color = "black") +
  facet_wrap(~LOCATION)
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-26-1.png)<!-- -->

If the fertility rate is below 2.1, then the population of the country
is decreasing. Lets add this 2.1 line to our figure (It is a horizontal
line, so we use geom\_hline & geom\_vline would be if you want a
vertical line). And wiht the linetype argument, you can change the
linetype.

``` r
ggplot(data = fertility_hun, mapping = aes(x = TIME, y = Value)) +
  geom_line(color = "black") + geom_hline(yintercept = 2.1, linetype = "dashed")
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-27-1.png)<!-- -->

With the geom\_smooth, you can add a trend line. If you set method =
“lm”, then you get a regression line.

``` r
ggplot(data = fertility_hun, mapping = aes(x = TIME, y = Value)) +
  geom_line(color = "black") + geom_smooth(method = "lm", se = FALSE)
```

![](DataAnalysis_session3_files/figure-gfm/unnamed-chunk-28-1.png)<!-- -->

I downloaded some more datasets form the OECD webpage, and merged the
datas into one dataset.

``` r
populationsize <- read_csv("https://stats.oecd.org/sdmx-json/data/DP_LIVE/.POP.TOT.MLN_PER.A/OECD?contentType=csv&detail=code&separator=comma&csv-lang=en&startPeriod=1960&endPeriod=2018")
lifeexp <- read.csv("https://stats.oecd.org/sdmx-json/data/DP_LIVE/.LIFEEXP.TOT.YR.A/OECD?contentType=csv&detail=code&separator=comma&csv-lang=en&startPeriod=1960&endPeriod=2019")
populationsize <-populationsize[,c(1,6,7)]
lifeexp <- lifeexp[,c(1,6,7)]
names(fertility)[3] <- "fertility"
names(populationsize)[3] <- "pop"
names(lifeexp)[3] <- "lifeexp"
names(lifeexp)[1] <- "LOCATION"

df <- fertility
df <- merge(df, populationsize)
df <- merge(df,lifeexp)
head(df)
```

``` 
  LOCATION TIME fertility     pop lifeexp
1      AUS 1960       3.5 10.2750    70.9
2      AUS 1961       3.5 10.5082    71.2
3      AUS 1962       3.4 10.7005    71.0
4      AUS 1963       3.3 10.9069    71.1
5      AUS 1964       3.2 11.1216    70.7
6      AUS 1965       3.0 11.3409    71.0
```

Now we have a panel data (= data from different countries for several
years & 3 variables), lets create a small animation with the gganimate
package. It can run only in the RStudio and you can save there is you
right click on the viewer pane and select save image.

``` r
# install.packages("gganimate")
library(gganimate)

ggplot(data = df, mapping = aes(x = lifeexp, y = fertility,
                                group = LOCATION, size=pop)) + 
  geom_point(color = "black", shape = 21, fill = "blue") + 
  gganimate::transition_time(TIME) + 
  labs(title = "year: {frame_time}") +
```
