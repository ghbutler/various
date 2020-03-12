The Binomial Distribution in R
================
Gabriel Butler
3/12/2020

### Introduction

This is a brief overview of how to work with the built-in functions for
the binomial distribution in R. My hope is that the visualizations will
make the theoretical concepts clearer to my students, and that the
additional code examples here will enable them to check their answers to
problems solved by hand calculation. I assume that the reader is already
familiar with the binomial distribution. This demo will be relatively
abstract compared to the tutorial in my lab book for my students, which
is linked
below.

<https://bookdown.org/gabriel_butler/ECON41Labs/tutorial-4-the-binomial-distribution.html>

### The binomial distribution in R

R has several built-in functions for the binomial distribution. They’re
listed in a table below along with brief descriptions of what each one
does.

| Binomial function                          | What it does                                                                     |
| ------------------------------------------ | -------------------------------------------------------------------------------- |
| `dbinom(x, size, prob)`                    | P(X = x), the probability that X = `x`                                           |
| `pbinom(q, size, prob, lower.tail = TRUE)` | P(X =\< q), the probability that X takes a value less than or equal to `q`       |
| `rbinom(n, size, prob)`                    | Generates numbers which follow a binomial distribution with the given parameters |

Note: There is one more binomial function, `qbinom()`, but we won’t be
using it in this course.

### Theoretical and empirical discrete probabilities

We can use `dbinom()` to calculate the theoretical probability given by
the binomial formula of an exact number of successes in some number of
Bernoulli trials.

For example, suppose we wanted to know the theoretical probability of
two successes in 10 trials with a probability of success of 0.5. This
would be given by the function call below.

``` r
dbinom(x = 2, size = 10, prob = 0.5)
```

    ## [1] 0.04394531

We can also simulate the same result using `rbinom()`, which randomly
generates numbers that follow a binomial distribution with
characteristics given by the user. Below is the result of a simulation
that is similar to the theoretical result above. We generate 10,000
Bernoulli trials of size 10 with 2 successes and a probability of
success of 0.5. The result is not exactly equal to the theoretical
probability above, but since the number of experiments is so large, it
is quite close to that result.

``` r
mean(rbinom(n = 10000, size = 10, prob = 0.5) == 2)
```

    ## [1] 0.0454

We can also visualize the result of this kind of simulation. The
theoretical and empirical probablities we calculated above are equal to
the areas inside of the turquoise bars of the plots below.

``` r
empirical_dat <- as.data.frame(rbinom(n = 10000, size = 10, prob = 0.5))
names(empirical_dat) <- c('successes')

theoretical_dat <- data.frame(successes = 0:10,
           discrete_probability = dbinom(0:10, 10, 0.5))

empirical_plot <- empirical_dat %>% ggplot() + 
  geom_histogram(aes(x = successes, 
                     y = stat(count / sum(count)),
                     fill = successes == 2), 
                 color = 'black',
                 binwidth = 1) +
  theme_bw() +
  labs(x = 'Number of successes in 10 trials',
       y = 'Proportion',
       title = '10,000 samples of b(10, 0.5)') +
  scale_x_continuous(breaks = seq(0, 10))

theoretical_plot <- theoretical_dat %>% ggplot() +
  geom_col(aes(successes, discrete_probability, fill = successes == 2), color = 'black') +
  theme_bw() +
  scale_x_continuous(breaks = seq(0, 10)) +
  labs(x = 'Number of successes',
       y = 'Theoretical probabilty',
       title = 'Probability density function',
       subtitle = 'b(10, 0.5)')

ggarrange(theoretical_plot, empirical_plot)
```

![](binomial-distribution-demo_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

### Theoretical and empirical lower tail probabilities

Sometimes we want to calculate the probability that a number of
successes will be less than, greater than, less than or equal to or less
than or greater to a certain value. There are a number of ways that we
can do this.

Suppose we want to know the theoretical probability of 4 or fewer
successes in 10 Bernoulli trials with a probability of success of 0.5.
This means that we want to calculate P(x \<= 4). This corresponds to the
following call of `pbinom()` and the alternative call of `dbinom()`
inside of `sum()`.

``` r
pbinom(4, size = 10, prob = 0.5)
```

    ## [1] 0.3769531

``` r
sum(dbinom(0:4, 10, 0.5))
```

    ## [1] 0.3769531

We can also simulate this result using `rbinom()` and visualize the
result.

``` r
mean(rbinom(n = 10000, size = 10, prob = 0.5) <= 4)
```

    ## [1] 0.3765

``` r
empirical_dat <- as.data.frame(rbinom(n = 10000, size = 10, prob = 0.5))
names(empirical_dat) <- c('successes')

theoretical_dat <- data.frame(successes = 0:10,
           discrete_probability = dbinom(0:10, 10, 0.5))

empirical_plot <- empirical_dat %>% ggplot() + 
  geom_histogram(aes(x = successes, 
                     y = stat(count / sum(count)),
                     fill = successes <= 4), 
                 color = 'black',
                 binwidth = 1) +
  theme_bw() +
  labs(x = 'Number of successes in 10 trials',
       y = 'Proportion',
       title = '10,000 samples of b(10, 0.5)') +
  scale_x_continuous(breaks = seq(0, 10))

theoretical_plot <- theoretical_dat %>% ggplot() +
  geom_col(aes(successes, discrete_probability, fill = successes <= 4), color = 'black') +
  theme_bw() +
  scale_x_continuous(breaks = seq(0, 10)) +
  labs(x = 'Number of successes',
       y = 'Theoretical probabilty',
       title = 'Probability density function',
       subtitle = 'b(10, 0.5)')

ggarrange(theoretical_plot, empirical_plot)
```

![](binomial-distribution-demo_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

### Theoretical and empirical upper tail probabilities

Calculating upper tail probabilities is a little trickier.

`pbinom()` has a `lower.tail` argument whose value can be set to `FALSE`
to calculate upper tail probabilities. However, this calculates the
theoretical probability of *greater* than a certain number of successes
and **not** greater than or equal to a certain number of successes.
I.e., it calculates P(X \> x), not P(X \>= x). This is one more thing
you need to remember when using `pbinom()` for upper tail probabilities.
An alternative is to use `dbinom()` together with `sum()` when
calculating P(X\>=x). Consider the examples below for calculating P(x \>
6), which is the same as P(x \>=
7).

``` r
pbinom(6, size = 10, prob = 0.5, lower.tail = FALSE)
```

    ## [1] 0.171875

``` r
sum(dbinom(7:10, 10, 0.5))
```

    ## [1] 0.171875

### Calculating probabilities of ranges of events between the tails of a distribution

Sometimes we want to calculate the probability that the number of
successes in a certain number of trials will be within some range that
doesn’t correspond to a certain tail probability. For example, suppose
we’re interested in the probability that there will be between 4 and 6
successes inclusive in 10 Bernoulli trials. I.e., we want to calculate
P(4 \>= x \<= 6).

The easiest way to calculate this is using `dbinom()` and `sum()`
together.

``` r
sum(dbinom(4:6, 10, 0.5))
```

    ## [1] 0.65625

``` r
theoretical_dat %>% ggplot() +
  geom_col(aes(successes, 
               discrete_probability, 
               fill = successes >= 4 & successes <= 6), color = 'black') +
  theme_bw() +
  scale_x_continuous(breaks = seq(0, 10)) +
  labs(x = 'Number of successes',
       y = 'Theoretical probabilty',
       title = 'Probability density function',
       subtitle = 'b(10, 0.5)')
```

![](binomial-distribution-demo_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

### Cumulative distribution functions (CDF)

We can also plot CDFs in R. Below are examples of theoretical and
empirical CDFs.

``` r
cdf_dat <- data.frame(successes = 0:10,
                      cumulative_probability = pbinom(0:10, 10, 0.5))

theoretical_cdf <- cdf_dat %>% ggplot() +
  geom_line(aes(successes, cumulative_probability), color = 'blue') +
  geom_point(aes(successes, cumulative_probability), color = 'blue') +
  theme_bw() +
  scale_x_continuous(breaks = seq(0, 10)) +
  labs(x = 'Number of successes',
       y = 'Cumulative probabilty',
       title = 'Cumulative distribution function',
       subtitle = 'n = 10, p = 0.5')

empirical_cdf <- data.frame(x = rbinom(n = 10000, size = 10, prob = 0.5)) %>% ggplot() +
  stat_ecdf(aes(x), geom = 'step', color = 'blue') +
  theme_bw() +
  scale_x_continuous(breaks = seq(0, 10)) +
  labs(x = 'Number of successes',
       y = 'Cumulative probabilty',
       title = 'Cumulative distribution function',
       subtitle = 'n = 10, p = 0.5')

ggarrange(theoretical_cdf, empirical_cdf)
```

![](binomial-distribution-demo_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->
