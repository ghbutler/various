Lab 1 demo
================
Gabriel Butler
2/21/2020

``` r
library(tidyverse)

video <- read.csv('video_test.csv')
```

### My research question

My research question is: How do responses to the question ‘Do you like
to play video games?’ vary between males and females in this survey?

### My findings

I found that a large percentage of students in this class at least
“somewhat” like video games. However, among people who like video
games “very much”, this view was much more common among men than women.
19% of respondents were men who liked video games “very much”, but only
5% of respondents were women who held such an attitude.

``` r
prop.table(table(video$sex, video$like))
```

    ##         
    ##          Never played No response Not at all Not really   Somewhat  Very much
    ##   Female   0.00000000  0.00000000 0.04395604 0.08791209 0.23076923 0.05494505
    ##   Male     0.01098901  0.01098901 0.03296703 0.05494505 0.27472527 0.19780220

My visualization makes the variation about views regarding video games
among men and women a lot clearer. It is quite obvious from the figure
below that negative or indifferent attitudes towards video games are
much more common among women than men, and that positive and
enthusiastic attitudes about video games are much more popular among men
than women in this
survey.

``` r
video %>% filter(like != c('No response', 'Never played')) %>% ggplot() + 
  geom_bar(aes(like, fill = sex), 
           color = 'black',
           position = 'fill') + 
  theme_bw() + 
  labs(y = 'Proportion', 
       title = 'Do you like to play video games?', 
       fill = 'Sex of respondent') + 
  theme(axis.text.x = element_text(angle = 45, 
                                   hjust = 1),
        axis.title.x = element_blank())
```

![](https://github.com/ghbutler/various/blob/master/unnamed-chunk-3-1.png?raw=true)<!-- -->

### Significance

These findings may be useful for a software company which produces video
games that is trying to figure out how to target their advertising. If
we assume that the sample of people who were surveyed is somehow
representative, then such a firm may believe it’s a good idea to put
their advertisements in places men are more likely to see them, such as
in commercials on television programs that are more popular with men
such as Monday Night Football or UFC matches.

The results may also help further explain the findings of some recent
research about declining male labor force participation. The authors of
this research attribute it to increases in the quality of the gaming
experience. However, it seems possible based on this survey that male
fondness of video games predates this
research.

<https://review.chicagobooth.edu/economics/2017/article/video-games-get-better-young-men-work-less-and-play-more>
