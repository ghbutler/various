nCov2019 package demo
================
Gabriel Butler
2/15/2020

### Introduction

This demo is based on a recent article from Towards Data Science about
an R package for acquiring data about the nCov2019 virus. The original
article is linked
below.

<https://towardsdatascience.com/an-r-package-to-explore-the-novel-coronavirus-590055738ad6>

In order to work with this data, we have to make sure we do a few
things.

First, we need to install the `remotes` package if we haven’t already
done so.

Next, we need to install the `nCov2019` package using the directions in
the Towards Data Science article.

Finally, we will load the `nCov2019` package in the usual way. Once we
do that, we can start downloading data.

### Working with the most recent nCov2019 data

The first function from the `nCov2019` package we will use is called
`get_nCov2019()`. This function allows us to download the most recent
batch of nCov2019 data for China and the world in English or Chinese.
This function does not download historical data.

To use this function, we should save the downloaded data as an object.
Here I’m going to call this object `ncov` because its meaning is obvious
and this short name makes it easy to type.

``` r
library(nCov2019)

ncov <- get_nCov2019(lang = 'en')
```

Next, let’s have a look at the `ncov` object.

``` r
ncov
```

    ## China (total confirmed cases): 68425
    ## last update: 2020-02-16 06:58:01

At first glance this function doesn’t seem to deliver much information.
All it tells us is the number of confirmed cases in China and the last
time the data was updated.

In order to squeeze more information out of `ncov`, we need to use
brackets.

``` r
ncov[]
```

    ##              name confirm suspect dead deadRate showRate heal healRate showHeal
    ## 1           Hubei   56249       0 1596     2.84    FALSE 5623    10.00     TRUE
    ## 2       Guangdong    1294       0    2     0.15    FALSE  386    29.83     TRUE
    ## 3           Henan    1212       0   13     1.07    FALSE  356    29.37     TRUE
    ## 4        Zhejiang    1162       0    0     0.00    FALSE  409    35.20     TRUE
    ## 5           Hunan    1001       0    2     0.20    FALSE  389    38.86     TRUE
    ## 6           Anhui     950       0    6     0.63    FALSE  232    24.42     TRUE
    ## 7         Jiangxi     913       0    1     0.11    FALSE  210    23.00     TRUE
    ## 8         Jiangsu     604       0    0     0.00    FALSE  160    26.49     TRUE
    ## 9       Chongqing     538       0    5     0.93    FALSE  184    34.20     TRUE
    ## 10       Shandong     532       0    2     0.38    FALSE  151    28.38     TRUE
    ## 11        Sichuan     470       0    1     0.21    FALSE  115    24.47     TRUE
    ## 12   Heilongjiang     425       0   11     2.59    FALSE   48    11.29     TRUE
    ## 13        Beijing     375       0    4     1.07    FALSE   97    25.87     TRUE
    ## 14       Shanghai     326       0    1     0.31    FALSE  124    38.04     TRUE
    ## 15          Hebei     291       0    3     1.03    FALSE   86    29.55     TRUE
    ## 16         Fujian     285       0    0     0.00    FALSE   66    23.16     TRUE
    ## 17        Guangxi     235       0    2     0.85    FALSE   40    17.02     TRUE
    ## 18        Shaanxi     232       0    0     0.00    FALSE   53    22.84     TRUE
    ## 19         Yunnan     168       0    0     0.00    FALSE   42    25.00     TRUE
    ## 20         Hainan     162       0    4     2.47    FALSE   38    23.46     TRUE
    ## 21        Guizhou     143       0    1     0.70    FALSE   36    25.17     TRUE
    ## 22         Shanxi     128       0    0     0.00    FALSE   46    35.94     TRUE
    ## 23        Tianjin     122       0    3     2.46    FALSE   37    30.33     TRUE
    ## 24       Liaoning     119       0    1     0.84    FALSE   29    24.37     TRUE
    ## 25          Gansu      90       0    2     2.22    FALSE   49    54.44     TRUE
    ## 26          Jilin      88       0    1     1.14    FALSE   25    28.41     TRUE
    ## 27       Xinjiang      70       0    1     1.43    FALSE    6     8.57     TRUE
    ## 28        Ningxia      70       0    0     0.00    FALSE   27    38.57     TRUE
    ## 29 Inner Mongolia      68       0    0     0.00    FALSE    6     8.82     TRUE
    ## 30      Hong Kong      56       0    1     1.79    FALSE    1     1.79     TRUE
    ## 31         Taiwan      18       0    0     0.00    FALSE    1     5.56     TRUE
    ## 32        Qinghai      18       0    0     0.00    FALSE   11    61.11     TRUE
    ## 33          Macau      10       0    0     0.00    FALSE    3    30.00     TRUE
    ## 34          Tibet       1       0    0     0.00    FALSE    1   100.00     TRUE

Now we are getting somewhere.

We can make this table more informative by reordering it. One variable
that’s worth taking a closer look at is `deadRate`, which seems like
it’s the number of nCov2019 deaths per 100,000 people at the
provincial level. Let’s see which 10 provinces are the ones with the
highest death rates in descending
    order.

``` r
ncov[] %>% arrange(desc(deadRate)) %>% head(10)
```

    ##            name confirm suspect dead deadRate showRate heal healRate showHeal
    ## 1         Hubei   56249       0 1596     2.84    FALSE 5623    10.00     TRUE
    ## 2  Heilongjiang     425       0   11     2.59    FALSE   48    11.29     TRUE
    ## 3        Hainan     162       0    4     2.47    FALSE   38    23.46     TRUE
    ## 4       Tianjin     122       0    3     2.46    FALSE   37    30.33     TRUE
    ## 5         Gansu      90       0    2     2.22    FALSE   49    54.44     TRUE
    ## 6     Hong Kong      56       0    1     1.79    FALSE    1     1.79     TRUE
    ## 7      Xinjiang      70       0    1     1.43    FALSE    6     8.57     TRUE
    ## 8         Jilin      88       0    1     1.14    FALSE   25    28.41     TRUE
    ## 9         Henan    1212       0   13     1.07    FALSE  356    29.37     TRUE
    ## 10      Beijing     375       0    4     1.07    FALSE   97    25.87     TRUE

The fact that Hubei is the province with the highest death rate isn’t
surprising. As we all know, Hubei is where the vast majority of nCov2019
cases are concentrated.

One thing that’s quite surprising in my opinion is that Heilongjiang is
the province with the second highest death rate in China as of this
writing. However, on February 4th, Harbin was one of many cities in
China in which movement restrictions were imposed on residents. I only
know about this because I lived in Harbin for a long time and I still
know people who live there who are now only allowed to leave their homes
once every other day in order to get food.

What about the provinces of Guangdong and Zhejiang? In the early days of
the outbreak these were the provinces in which the virus was most
virulent outside of Hubei. Quarantine restrictions that were imposed in
places like Hangzhou and Taizhou got a lot of attention too, but these
provinces aren’t in the top ten, at least in terms of their current
nCov2019 death rates. So how do they rank in these terms?

``` r
snip <- ncov[] %>% arrange(desc(deadRate))

snip[c(21, 23), ]
```

    ##         name confirm suspect dead deadRate showRate heal healRate showHeal
    ## 21 Guangdong    1294       0    2     0.15    FALSE  386    29.83     TRUE
    ## 23  Zhejiang    1162       0    0     0.00    FALSE  409    35.20     TRUE

``` r
mean(as.numeric(snip$deadRate))
```

    ## [1] 0.7476471

Guangdong and Zhejiang are ranked 21st and 23rd respectively in terms of
their current death rates for nCov2019, and both provinces have death
rates that are significantly below the current average. (11 provinces
have had no deaths at all.)

### Working with historical nCov2019 data

In order to download older data, we have to use the `load_nCov2019()`
function. We’re going to download this data in English and save it as an
object called `historical`.

``` r
historical <- load_nCov2019(lang = 'en')
```

Now let’s have a look at what information `historical` contains.

``` r
historical
```

    ## nCov2019 historical data 
    ## last update: 2020-02-13

It’s pretty similar to the data object we worked with in the last
section. At first glance, it tells us even less than that one did:
nothing more than the date of the most recent update. Once again, we
must use bracketing in order squeeze more information out of it. (We are
focusing on columns 1-6 and 9-11 because the other columns are in
Chinese.)

``` r
head(historical[][c(1:6, 9:11)])
```

    ##   province          city       time cum_confirm cum_heal cum_dead confirm dead
    ## 1 Shanghai      Shanghai 2020-02-13         318       62        1           NA
    ## 2   Yunnan        Yunnan 2020-02-13         162       27        0           NA
    ## 3   Yunnan       Kunming 2020-02-13          48        8        0           NA
    ## 4   Yunnan      Zhaotong 2020-02-13          19        2        0           NA
    ## 5   Yunnan Xishuangbanna 2020-02-13          15        2        0           NA
    ## 6   Yunnan        Qujing 2020-02-13          13        1        0           NA
    ##   heal
    ## 1     
    ## 2     
    ## 3     
    ## 4     
    ## 5     
    ## 6

There are two columns in `historical` which are of interest to us right
now: `cum_confirm` and `cum_heal`. The first one tells us the number of
cumulative confirmed cases since data started being compiled. The second
tells us the number of people who have been cured since data started
being complied.

As `dim()` tells us below, this is a pretty big object: well over 7,000
observations and 19 variables. And as the printout above suggests,
`historical` contains somewhat granular information for all of China.
There must be some way that we can cut this down to size to make it more
relevant to us.

``` r
dim(historical[])
```

    ## [1] 7258   19

### Working with historical nCov2019 data fom Nanjing

We can filter our data to extract information about the virus in
Nanjing.

First, let’s try filtering it for Jiangsu only, again limiting our
filtering to English language
    data.

``` r
head(historical['Jiangsu', ][c(1:6, 9:11)])
```

    ##     province        city       time cum_confirm cum_heal cum_dead confirm dead
    ## 165  Jiangsu      Suzhou 2020-02-13          85       17        0           NA
    ## 166  Jiangsu     Nanjing 2020-02-13          91       26        0           NA
    ## 167  Jiangsu      Xuzhou 2020-02-13          76       17        0           NA
    ## 168  Jiangsu     Huai'an 2020-02-13          56        6        0           NA
    ## 169  Jiangsu        Wuxi 2020-02-13          51        9        0           NA
    ## 170  Jiangsu Lianyungang 2020-02-13          45       10        0           NA
    ##     heal
    ## 165     
    ## 166     
    ## 167     
    ## 168     
    ## 169     
    ## 170

``` r
dim(historical['Jiangsu',][c(1:6, 9:11)])
```

    ## [1] 274   9

This is too much information for us right now. We need to filter it
further so that it only contains data for Nanjing. We can do that using
`subset()`, and we’re going to save the result as an object called `nj`.

``` r
nj <- subset(historical['Jiangsu',], city == 'Nanjing')[c(1:6, 9:11)]

nj
```

    ##      province    city       time cum_confirm cum_heal cum_dead confirm dead
    ## 166   Jiangsu Nanjing 2020-02-13          91       26        0           NA
    ## 529   Jiangsu Nanjing 2020-02-12          90       24        0           NA
    ## 891   Jiangsu Nanjing 2020-02-11          87       22        0           NA
    ## 1257  Jiangsu Nanjing 2020-02-10          84       22        0           NA
    ## 1620  Jiangsu Nanjing 2020-02-09          78       22        0           NA
    ## 1982  Jiangsu Nanjing 2020-02-08          71       20        0           NA
    ## 2343  Jiangsu Nanjing 2020-02-07          65       15        0           NA
    ## 2703  Jiangsu Nanjing 2020-02-06          57       13        0           NA
    ## 3065  Jiangsu Nanjing 2020-02-05          52       11        0       5   NA
    ## 3409  Jiangsu Nanjing 2020-02-04          47        4        0       3   NA
    ## 3726  Jiangsu Nanjing 2020-02-03          44        2        0       4   NA
    ## 4060  Jiangsu Nanjing 2020-02-02          40        2        0       5   NA
    ## 4414  Jiangsu Nanjing 2020-02-01          35        2        0       7   NA
    ## 4750  Jiangsu Nanjing 2020-01-31          28        2        0       3   NA
    ## 5239  Jiangsu Nanjing 2020-01-30          25        0        0       6   NA
    ## 5559  Jiangsu Nanjing 2020-01-29          19        0        0       5   NA
    ## 5796  Jiangsu Nanjing 2020-01-28          14        0        0       4   NA
    ## 6126  Jiangsu Nanjing 2020-01-27          10        0        0       3   NA
    ## 6420  Jiangsu Nanjing 2020-01-26           7        0        0       0   NA
    ## 6654  Jiangsu Nanjing 2020-01-25           7        0        0       3   NA
    ## 6869  Jiangsu Nanjing 2020-01-24           4        0        0       1   NA
    ## 7030  Jiangsu Nanjing 2020-01-23           3        0        0       3   NA
    ##      heal
    ## 166      
    ## 529      
    ## 891      
    ## 1257     
    ## 1620     
    ## 1982     
    ## 2343     
    ## 2703     
    ## 3065    7
    ## 3409     
    ## 3726     
    ## 4060    0
    ## 4414     
    ## 4750     
    ## 5239    0
    ## 5559     
    ## 5796     
    ## 6126     
    ## 6420     
    ## 6654     
    ## 6869     
    ## 7030

This is pretty interesting so far. Now we have information about
cumulative confirmed cases and cumulative cured cases in Nanjing between
January 23rd and February 13th.

However, it would be nice to know what the current number of confirmed
cases is on each day. We can calculate that by taking the difference
between `cum_confirm` and `cum_heal` and plotting it. This is done
below.

``` r
nj %>% ggplot() + 
  geom_point(aes(time, cum_confirm, color = 'cum_confirm'), alpha = 0.3) +
  geom_line(aes(time, cum_confirm, color = 'cum_confirm'), alpha = 0.3) +
  geom_point(aes(time, cum_heal, color = 'cum_heal'), alpha = 0.3) +
  geom_line(aes(time, cum_heal, color = 'cum_heal'), alpha = 0.3) +
  geom_point(aes(time, cum_confirm - cum_heal, color = 'cum_confirm - cum_heal')) +
  geom_line(aes(time, cum_confirm - cum_heal, color = 'cum_confirm - cum_heal')) +
  theme_bw() +
  theme(legend.position = c(0.2, 0.8),
        legend.background = element_blank(),
        axis.text.x = element_text(angle = 90)) +
  labs(title = 'nCov2019 cases in Nanjing, Jiangsu, China',
       x = '',
       y = 'Number of people') +
  scale_color_manual(values = c('red', 'blue', 'green'), 
                     name = 'Types of cases',
                     labels = c('Cumulative confirmed',
                                'Confirmed but not cured',
                                'Cumulative cured')) +
  scale_x_date(date_breaks = '1 day', 
               date_labels = '%m-%d',
               limits = c(as.Date('2020-01-23'), as.Date('2020-02-13')))
```

![](https://github.com/ghbutler/various/blob/master/unnamed-chunk-13-1.png?raw=true)<!-- -->

The line that matters most here is the blue one because it plots the
number of confirmed cases in Nanjing on each day since data started
being recorded. It’s the difference between cumulative confirmed cases
and cumulative cured cases because there have been no deaths in Nanjing
so far, which is something that hopefully will not change. (Otherwise
deaths would also have to be subtracted from cumulative confirmed cases
to get the number of confirmed cases remaining on each day.)

The blue line tells us something that all of us who have been watching
the numbers in Nanjing everyday have known in our guts but maybe haven’t
been sure about: there is a trend, and that trend is that the number of
confirmed cases is increasing.
