Functional programming with nCov2019 data
================
Gabriel Butler
2/22/2020

### Introduction

The purpose of this demo is to provide my students with another example
of functional programming that will hopefully make Lab 2 a little easier
for them. My hope is that its focus on nCov2019 data will make these
concepts especially memorable. It is also meant to be a follow-up of the
demo of the `nCov2019` package I created recently.

### Review

Before we start, we need to load the packages we’ll be using in this
demo and the data we’ll be analyzing.

``` r
library(tidyverse)
library(nCov2019)
library(ggpubr)

data <- load_nCov2019(lang = 'en')
```

Recall also that last time one of the subsets of the nCov2019 data we
were interested in was data from the city of Nanjing. This is done
below. Notice that the only value for the `city` variable in the
dataframe below is `"Nanjing"`.

The last demo ended with a visualization of the Nanjing data. It is
reproduced below with more recent data.

Notice that in the visualization below it appears that a new trend has
emerged: cases that have been confirmed but not cured in Nanjing are
trending downwards. Last time we visualized a batch of this data these
cases were trending upwards. The line for cumulative confirmed cases has
mostly been flat for a while too, indicating that new cases aren’t
really popping up anymore. And finally, Nanjing still hasn’t had a
single death since this crisis started.

``` r
nj <- subset(data['Jiangsu',], city == 'Nanjing')

str(nj)
```

    ## 'data.frame':    27 obs. of  7 variables:
    ##  $ time       : Date, format: "2020-01-25" "2020-01-26" ...
    ##  $ province   : chr  "Jiangsu" "Jiangsu" "Jiangsu" "Jiangsu" ...
    ##  $ city       : chr  "Nanjing" "Nanjing" "Nanjing" "Nanjing" ...
    ##  $ cum_confirm: int  4 7 10 14 19 25 28 35 40 44 ...
    ##  $ cum_heal   : int  0 0 0 0 0 2 2 2 2 4 ...
    ##  $ cum_dead   : int  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ suspected  : int  0 0 0 0 0 0 0 0 0 0 ...

``` r
nj_plt <- nj %>% ggplot() + 
  geom_point(aes(time, cum_confirm, color = 'cum_confirm'), alpha = 0.3) +
  geom_line(aes(time, cum_confirm, color = 'cum_confirm'), alpha = 0.3) +
  geom_point(aes(time, cum_heal, color = 'cum_heal'), alpha = 0.3) +
  geom_line(aes(time, cum_heal, color = 'cum_heal'), alpha = 0.3) +
  geom_point(aes(time, cum_dead, color = 'cum_dead'), alpha = 0.3) +
  geom_line(aes(time, cum_dead, color = 'cum_dead'), alpha = 0.3) +
  geom_point(aes(time, cum_confirm - cum_heal - cum_dead, color = 'cum_confirm - cum_heal')) +
  geom_line(aes(time, cum_confirm - cum_heal - cum_dead, color = 'cum_confirm - cum_heal')) +
  theme_bw() +
  theme(legend.position = c(0.2, 0.8),
        legend.background = element_blank(),
        axis.text.x = element_text(angle = 90)) +
  labs(title = 'nCov2019 cases in Nanjing, Jiangsu, China',
       x = '',
       y = 'Number of people') +
  scale_color_manual(values = c('red', 'blue', 'black', 'green'), 
                     name = 'Types of cases',
                     labels = c('Cumulative confirmed',
                                'Confirmed but not cured',
                                'Cumulative dead',
                                'Cumulative cured')) +
  scale_x_date(date_breaks = '1 day', 
               date_labels = '%m-%d')

nj_plt
```

![](https://github.com/ghbutler/various/blob/master/unnamed-chunk-2-1.png?raw=true)<!-- -->

The graph we produced in the last tutorial for the `nCov2019` package
was pretty cool and informative, at least in my opinion. It would also
be great to produce graphics like these for other cities in China which
interest us. So how can we do that? We have two choices.

The first is we can copy the code from the filtered data and graph above
and edit it line by line until we produce the next graph that we want.
But this will be pretty tedious if we’re interested in many cities, and
we’re also likely to make mistakes while we’re doing this over and over.

The second is the approach we’re going to use in this tutorial:
functional programming. We’re going to write a function to produce
graphs for different cities because once we get our function working, it
will enable to produce new graphs much faster and without making any
silly mistakes.

Before proceeding, it’s worth sharing a quote from Hadley Wickham and
Garrett Grolemund in their book *R for Data Science* about when you
should write a function. It’s a good rule of thumb that you should
commit to your own memory.

> You should consider writing a function whenever you’ve copied and
> pasted a block of code more than twice (i.e. you now have three copies
> of the same code).

You can read more about functions in chapter 19 of the free online
version of their book, which is linked below.

<https://r4ds.had.co.nz/functions.html>

### Our first attempt at a function

The visualization we produced for Nanjing was very detailed and
consisted of over 30 lines of code. As we build our function we’re going
to use a simplified version of that plotting code to make the
programming process faster and easier. Once we get our function working,
we’ll add the rest.

We’re going to call our plotting function `plot_cases()` and make it a
function of three variables with pretty obvious meanings: `city`,
`province` and `data`, where the last variable is the historical data
loaded from `nCov2019` using `load_nCov2019()`. For now we’re going to
focus on plotting cumulative confirmed cases, or `cum_confirm`. We’re
also going to test the fuction.

``` r
plot_cases <- function(city, province, data){
  
  filtered_data <- subset(data[province,], city == city)
  
  plt <- filtered_data %>% ggplot() +
    geom_point(aes(time, cum_confirm)) +
    geom_line(aes(time, cum_confirm)) +
    theme_bw()
  
  plt
}

plot_cases('Nanjing', 'Jiangsu', data)
```

![](https://github.com/ghbutler/various/blob/master/unnamed-chunk-3-1.png?raw=true)<!-- -->

The result we got is pretty obviously not what we are looking for. What
went wrong? Perhaps comparing this result to the original example on
which it is based will provide some clues. This is done below.

When comparing these graphics, we need to remember that we should be
focusing on the red line in the left figure. The line in the right
figure should have the same shape, but it doesn’t.

How do these lines differ? Well, for one thing, the line in the right
figure has far more dots than the red line in the left figure. This
strongly suggests that there’s something wrong with the data that’s
being used to plot the function.

``` r
ggarrange(nj_plt, plot_cases('Nanjing', 'Jiangsu', data))
```

![](https://github.com/ghbutler/various/blob/master/unnamed-chunk-4-1.png?raw=true)<!-- -->

### Debugging our function

In order to debug our function, we’re going to have to take it apart.
We’re going to comment out our plotting code and replace the final
line with the local `filtered_data` variable so that when we run this
function again, it will enable us to examine the data that’s being
produced inside of the function.

The result we get reveals our problem: although we’re trying to filter
the data by city after filtering it by province inside of the function,
it appears to only be filtering it by province. Why is this happening?
And what can we do about it?

``` r
plot_cases <- function(city, province, data){
  
  filtered_data <- subset(data[province,], city == city)
  
  #plt <- filtered_data %>% ggplot() +
  #  geom_point(aes(time, cum_confirm)) +
  #  geom_line(aes(time, cum_confirm)) +
  #  theme_bw()
  
  filtered_data
}

head(plot_cases('Nanjing', 'Jiangsu', data))
```

    ##           time province        city cum_confirm cum_heal cum_dead suspected
    ## 110 2020-01-25  Jiangsu Lianyungang           1        0        0         0
    ## 111 2020-01-25  Jiangsu    Yangzhou           1        0        0         0
    ## 112 2020-01-25  Jiangsu     Nantong           1        0        0         0
    ## 113 2020-01-25  Jiangsu        Wuxi           1        0        0         0
    ## 114 2020-01-25  Jiangsu    Yancheng           1        0        0         0
    ## 115 2020-01-25  Jiangsu      Suqian           1        0        0         0

The main reason why this is happening is because there is a variable
called `city` inside of our `data` object. When we try to use this
variable name again inside of our function, R can’t tell the difference
between the local version of this variable and the global version of it.

One potential solution is we can rename the `city` argument inside of
the function. When we try that, we see that our funciton now only
returns data for Nanjing. This is what we wanted all along, at least
with regard to our filtered data.

``` r
plot_cases <- function(city, province, data){
  
  place <- city
  
  filtered_data <- subset(data[province,], city == place)
  
  #plt <- filtered_data %>% ggplot() +
  #  geom_point(aes(time, cum_confirm)) +
  #  geom_line(aes(time, cum_confirm)) +
  #  theme_bw()
  
  filtered_data
  
}

head(plot_cases('Nanjing', 'Jiangsu', data))
```

    ##            time province    city cum_confirm cum_heal cum_dead suspected
    ## 119  2020-01-25  Jiangsu Nanjing           4        0        0         0
    ## 227  2020-01-26  Jiangsu Nanjing           7        0        0         0
    ## 674  2020-01-28  Jiangsu Nanjing          10        0        0         0
    ## 850  2020-01-29  Jiangsu Nanjing          14        0        0         0
    ## 1335 2020-01-30  Jiangsu Nanjing          19        0        0         0
    ## 1439 2020-01-31  Jiangsu Nanjing          25        2        0         0

Now we’re going to test our plotting code to make sure it works.

``` r
plot_cases <- function(city, province, data){
  
  place <- city
  
  filtered_data <- subset(data[province,], city == place)
  
  plt <- filtered_data %>% ggplot() +
    geom_point(aes(time, cum_confirm)) +
    geom_line(aes(time, cum_confirm)) +
    theme_bw()
  
  plt
  
}

plot_cases('Nanjing', 'Jiangsu', data)
```

![](https://github.com/ghbutler/various/blob/master/unnamed-chunk-7-1.png?raw=true)<!-- -->

### The final version of our function

Now that we’ve debugged our basic function, producing one which
generates plots like the one in the last tutorial is pretty easy. We
just need to add more plotting layers and label it a bit differently.
Below we can see an example of a plot for Nanjing that is generated
using our plotting function this time. It’s basically indistinguishable
from the single plot that inspired it.

``` r
plot_cases <- function(city, province, data){
  
  place <- city
  
  filtered_data <- subset(data[province,], city == place)
  
  plt <- filtered_data %>% ggplot() + 
    geom_point(aes(time, cum_confirm, color = 'cum_confirm'), alpha = 0.3) +
    geom_line(aes(time, cum_confirm, color = 'cum_confirm'), alpha = 0.3) +
    geom_point(aes(time, cum_heal, color = 'cum_heal'), alpha = 0.3) +
    geom_line(aes(time, cum_heal, color = 'cum_heal'), alpha = 0.3) +
    geom_point(aes(time, cum_dead, color = 'cum_dead'), alpha = 0.3) +
    geom_line(aes(time, cum_dead, color = 'cum_dead'), alpha = 0.3) +
    geom_point(aes(time, cum_confirm - cum_heal - cum_dead, color = 'cum_confirm - cum_heal')) +
    geom_line(aes(time, cum_confirm - cum_heal - cum_dead, color = 'cum_confirm - cum_heal')) +
    theme_bw() +
    theme(legend.position = c(0.2, 0.8),
          legend.background = element_blank(),
          axis.text.x = element_text(angle = 90)) +
    labs(title = paste('nCov2019 cases in', city, ',', province, ',', 'China'),
         x = '',
         y = 'Number of people') +
    scale_color_manual(values = c('red', 'blue', 'black', 'green'), 
                       name = 'Types of cases',
                       labels = c('Cumulative confirmed',
                                  'Confirmed but not cured',
                                  'Cumulative dead',
                                  'Cumulative recovered')) +
    scale_x_date(date_breaks = '1 day', 
                 date_labels = '%m-%d')
  
  plt
}

plot_cases('Nanjing', 'Jiangsu', data)
```

![](https://github.com/ghbutler/various/blob/master/unnamed-chunk-8-1.png?raw=true)<!-- -->

### Using our function for other Chinese cities

Let’s not forget that this function was created to make it easy for us
to rapidly generate plots for other cities in China which interest us
besides Nanjing. Your interests may vary, but here are some of mine.

First, let’s have a look at Huanggang, Hubei. Those of you who remember
what I told you about myself on the first day of class when I introduced
myself may remember that the first place I ever lived in China was this
city. I had a job as an English teacher at Huanggang Normal University.
Although I only stayed there for one semester, it was quite memorable
because back then Huanggang was still very rural, although it was also
developing very rapidly.

For the first few weeks of the coronavirus crisis, Huanggang was the
city in China with the most cases besides Wuhan. Now it’s the third
highest because Xiaogan has even more.

How is Huanggang doing according to the most recent data in the
`nCov2019` package? We can get the answer with a single line of code.
Judging from the plot below, although the situation in Huanggang is
still quite severe, it looks like the worst of it may be over.

``` r
plot_cases('Huanggang', 'Hubei', data)
```

![](https://github.com/ghbutler/various/blob/master/unnamed-chunk-9-1.png?raw=true)<!-- -->

After my time in Huanggang, I moved to Zhengzhou. I wanted to move to a
big city and that was the first one where I found a job, so off I went,
once again to work as an English teacher at a university. I only stayed
in Zhengzhou for one semester too, but I had a great time there and I
made some friends that I still have today.

So how is Zhengzhou doing now? Henan is one of the provinces besides
Hubei in which coronavirus was quite virulent. Judging from the plot
below, Zhengzhou is getting things under control too.

``` r
plot_cases('Zhengzhou', 'Henan', data)
```

![](https://github.com/ghbutler/various/blob/master/unnamed-chunk-10-1.png?raw=true)<!-- -->

Another city I want to look at is Harbin. I moved to Harbin after my
semester in Zhengzhou and I stayed there for about five and a half
years, working at three different English schools, though I spent about
four years at just one.

How has Harbin been doing in this crisis? Judging from the plot below,
they seem to have had the most trouble of all getting their confirmed
cases under control, at least among cities we’ve looked at so far.

``` r
plot_cases('Harbin', 'Heilongjiang', data)
```

![](https://github.com/ghbutler/various/blob/master/unnamed-chunk-11-1.png?raw=true)<!-- -->

The last city I want to consider is Shenzhen, a city I’m quite fond of
and have spent a great deal of time in and once considered moving to.
Judging from the plot below, Shenzhen has its coronavirus problem under
control and if the current trend continues, perhaps it will be gone from
the city in a couple of more weeks.

``` r
plot_cases('Shenzhen', 'Guangdong', data)
```

![](https://github.com/ghbutler/various/blob/master/unnamed-chunk-12-1.png?raw=true)<!-- -->

There are lots of factors that influence the differing rates of success
against coronavirus in the cities we’ve considered here. One is
certainly level of economic development. Cities like Nanjing and
Shenzhen are more developed than places like Zhengzhou and Harbin, which
likely influences the level of development of their healthcare systems
as well. More developed cities tend to have better hospitals and better
doctors, and during a national public health emergency like the one now
unfolding across China, it’s not surprising to observe that more
developed places seem to be having more success fighting the virus than
less developed ones.

### Going further

Now you can use `plot_cases()` on your own to explore available
`nCov2019` data for cities that interest you.

Feel free to consider exploring the `nCov2019` data on your own using
methods and goals of your own choosing, and please share anything
interesting that you discover with the rest of the class.

Consider modifying the working plotting function that was created in
this tutorial so that it returns a plot that’s written in Chinese
instead of English. Remember that `nCov2019` can download both English
and Chinese language data, so making these changes should be pretty
easy.
