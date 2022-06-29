Activity 11 - Simulation
================
Jackie Fountain

## The Central Limit Theorem

From your introductory statistics course, recall that the **Central
Limit Theorem** tells us about the distribution of a sample mean, which
is called the sampling distribution. As sample size becomes sufficiently
large, the sampling distribution approaches a normal distribution and
the standard deviation of the sampling distribution decreases.

In this activity, we will explore these findings with a statistical
simulations. **For this activity, we will use a seed of 518.**

Note that when you knit your report, your results will always be the
same; however, if you re-run code chunks individually, they use
different seeds. One way to make sure that you get similar results each
time is to always run all previous code chunks ![run all chunks above
icon](README-img/run-above-icon.png) before running the current code
chunk ![run current chunk icon](README-img/run-current-icon.png).

## Set-up

For this activity, you will need the `{tidyverse}`.

**delete this line and load `{tidyverse}`**

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.4     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.2     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   1.4.0     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

## Exploration

### Normally distributed population

Below I generate 1,000 samples from a Normally distributed random
variable with a mean 50 and standard deviation 10 and constructed a
*histogram* and overlay a line for the distribution.

``` r
norm_samp_1000 <- tibble(
  values = rnorm(n = 1000, mean = 50, sd = 10)
)

ggplot(norm_samp_1000, aes(x = values)) + 
  geom_histogram(aes(y = ..count.. / sum(..count..)),       # plot proportion instead of count
                 fill = "white",
                 color = "black",
                 binwidth = 3) +                            # specify how big bins should be
  stat_function(fun = function(x)                           # some rescaling "magic" to get correct densities
                      dnorm(x, mean = 50, sd = 10) * 3,     # see: https://stackoverflow.com/a/36344354
                color = "#0065a4",                          # laker-blue is #0065a4
                size = 2,                                   # make line a little thicker
                alpha = 0.4) +                                
  geom_vline(xintercept = mean(norm_samp_1000$values),      # plot vline at mean of sample
             color = "#a43f00",                             # pick a color to contrast blue
             size = 2) +
  theme_minimal() +                                         # get ride of chart junk
  labs(y = "proportion")                                    # rename y axis
```

![](activity11-simulation_files/figure-gfm/norm-samp-1.png)<!-- -->

Using `norm_samp_1000`, obtain the mean and standard deviation of this
sample?

``` r
 mean(norm_samp_1000 $ values)
```

    ## [1] 50.19228

``` r
 sd(norm_samp_1000 $ values)
```

    ## [1] 9.59245

Now, create a new simulation. In this you will obtain a sample of size
30 from from a Normally distributed variable with a mean 50 and standard
deviation 10, and find the mean and standard deviation. How do the mean
and standard deviation compare to your answers above?

``` r
norm_samp_30 <- tibble(
  values = rnorm(n = 30, mean = 50, sd = 10)
)
 mean(norm_samp_30 $ values)
```

    ## [1] 47.78609

``` r
 sd(norm_samp_30 $ values)
```

    ## [1] 9.87055

``` r
 #smaller sample size values are a bit further off from the true mean and sd
```

### Non-normally distributed population

Using a similar process, obtain Exponentially distributed variable
(`?rexp`) with a *rate* of 0.02. Obtain a sample of 1,000 observations
and a sample of 30 observations. Then, calculate the mean and standard
deviation of each sample and discuss how the values are similar and how
they differ.

``` r
exp_samp_30 <- tibble(
  values = rexp(n=30, r=.02)
)
 mean(exp_samp_30 $ values)
```

    ## [1] 42.41691

``` r
 sd(exp_samp_30 $ values)
```

    ## [1] 35.07526

``` r
exp_samp_1000 <- tibble(
  values = rexp(n=1000, r=.02)
)
 mean(exp_samp_1000 $ values)
```

    ## [1] 48.1339

``` r
 sd(exp_samp_1000 $ values)
```

    ## [1] 49.6146

![](README-img/noun_pause.png) **Planned Pause Point**: If you have any
questions, contact your instructor. Otherwise feel free to continue on.

## Many distributions

We previously looked at one distribution at a time. It would be nice to
explore multiple distributions simulataneouly. To do this, we will
create a new function. Yay!

Write a function (say, `generate_n_samples`) that takes `n` (how many
values to sample) as its argument. This function should produce a
dataset containing four columns: one that generates data from a
**Normal** distribution (parameters mean and standard deviation), one
from a **Exponential** distribution (parameter rate), one from a
**Chi-square**
distribution`(parameter degrees of freedom), and one from a **Uniform** distribution (parameters minimum and maximum). This function should produce a dataset with`n\`
rows.

You should **not** use the default parameter options for these
distributions (e.g., your Normal data should not have a mean of 0 or a
standard deviation of 1). Test your function with `n = 30` and
`n = 1000`.

``` r
generate_n_samples<-function(n,mean,sd,r,df,min,max){
  tibble(
    norm_values=rnorm(n=n,mean=mean, sd=sd),
    exp_values = rexp(n=n, r=r),
    chisq_values=rchisq(n=n,df=df),
    uniform_values=runif(n=n,min=min,max=max)
  )
}
generate_n_samples(1,5,.05,4,2,1,11)
```

    ## # A tibble: 1 x 4
    ##   norm_values exp_values chisq_values uniform_values
    ##         <dbl>      <dbl>        <dbl>          <dbl>
    ## 1        4.97      0.113         1.76           10.6

Keep the following information in mind for the rest of this activity.
The mean and standard deviation for each of the general distributions we
are using in this activity are (using the R function arguments):

-   [Normal
    distribution](https://en.wikipedia.org/wiki/Normal_distribution):
    mean = `mean`, standard deviation = `sd`
-   [Exponential
    distribution](https://en.wikipedia.org/wiki/Exponential_distribution):
    mean = 1 / `rate`, standard deviation = 1 / `rate`
-   [Chi-square
    distribution](https://en.wikipedia.org/wiki/Chi-squared_distribution):
    mean = `df`, standard deviation = sqrt(2 \* `df`)
-   [Uniform
    distribution](https://en.wikipedia.org/wiki/Continuous_uniform_distribution):
    (`min` + `max`) / 2, standard deviation = (`max` - `min`) / sqrt(12)

Complete the table below with the **mean and standard deviation** values
for your specific distributions. These are the “population values”.

| Distribution | Mean | SD   |
|--------------|------|------|
| Normal       | 5    | .05  |
| Exponential  | .25  | .25  |
| Chi-square   | 2    | 2    |
| Uniform      | 6    | 2.88 |

Below I provide you with a function that calculates the sample mean for
each column/distribution.

``` r
sample_mean <- function(.data){
  .data %>% 
    summarise(across(everything(), mean, .names = "mean_{.col}"))
}
```

First, verify that you know what each line of this function is doing.
Then, use this function in addition to your multiple distribution sample
generator from above to calculate the means for `n = 30` and `n = 1000`
Discuss how the means compares for each distribution. Note that it
doesn’t make sense to compare means/standard deviations across the
distributions.

``` r
sample_mean(generate_n_samples(30,5,.05,4,2,1,11))
```

    ## # A tibble: 1 x 4
    ##   mean_norm_values mean_exp_values mean_chisq_values mean_uniform_values
    ##              <dbl>           <dbl>             <dbl>               <dbl>
    ## 1             4.98           0.269              2.11                6.28

``` r
sample_mean(generate_n_samples(1000,5,.05,4,2,1,11))
```

    ## # A tibble: 1 x 4
    ##   mean_norm_values mean_exp_values mean_chisq_values mean_uniform_values
    ##              <dbl>           <dbl>             <dbl>               <dbl>
    ## 1             5.00           0.259              2.08                6.00

## Many samples

We previously looked at taking one sample at a time. To explore the
Central Limit Theorem (CLT), we need to generate multiple samples. The
`BASE::replicate`, `purrr::rerun`, or `purr::map_*` functions are
convenient ways to generate multiple samples of data.

    # Each of these generate 1,000 samples of n = 30 from a normal(mean = 50, sd = 10)

    # BASE::replicate()
    ## simplify = FALSE keeps the results as a list 
    ## rather than coercing them to an array/matrix
    replicate(1000, rnorm(30, mean = 50, sd = 10), simplify = FALSE)

    # purrr::rerun
    rerun(1000, rnorm(30, mean = 50, sd = 10))

    # This could also be done in purr::map 
    map(1:1000, ~rnorm(30, mean = 50, sd = 10))

Continuing to work with your multiple distribution sample generator from
above, generate 1,000 samples from the four distributions each with size
30. We will want to continue to work with this list so be sure to assign
these samples to an appropriately named object (say,
`multiple_samples`).

``` r
multiple_samples<-rerun(1000,generate_n_samples(30,5,.05,4,2,1,11))
```

Calculate the sample mean of each sample using the appropriate `map`
function and my `sample_mean` function to produce a *data frame* of
multiple sample means from your multiple samples object. Assign these
sample means to an appropriately named object.

``` r
data.frame(map(1:1000,~sample_mean(generate_n_samples(n=30, param1=1,param2=1))))
```

    ## Error in generate_n_samples(n = 30, param1 = 1, param2 = 1): unused arguments (param1 = 1, param2 = 1)

Restructure your previous sample means R object to, instead of having
1,000 rows and 4 columns, have 4,000 rows and 2 columns where the
columns are named `distribution` and `means`. Assign this restructured
data to an appropriately named object.

Using the restructured data, create a histogram for each `distribution`
using a *facet* layer.

Comment on each of shape of each of the mean plots above. How was the
distribution of the sample means different than the distribution of the
population? Note, looking at the wiki pages linked early in this
activity will help you compare your sample mean distributions to the
population distributions.

Using the restructured data, obtain the mean and standard deviation
**for each** of the distributions.

Comment on how each mean compares to the “population values” mean that
you calculated above.

Comment on how each standard deviation compares to the “population
values” standard deviation that you calculated above.

![](README-img/noun_pause.png) **Planned Pause Point**: If you have any
questions, contact your instructor. Otherwise feel free to continue on.

## Larger sample size

You previously explored how the distribution of sample means for
`n = 30` behaves for four specific distributions. Now, I will do this
for `n = 100`. Update the parameter arguments in
`distribution_exploration` with the values that you have been working
with. You can explore my quickly-assembled function if you are
interested. After you run the code below, be sure to click through the
different viewer windows to see the plots and summary tables. The first
view widow should simply tell you “`stat_bin()` using `bins = 30`. Pick
better value with `binwidth`.”

``` r
source(here::here("functions", "helper.R"))

distribution_exploration(n = 100, rep = 1000, mean = 50, sd = 10, rate = 0.2, df = 4, min = 20, max = 40)
```

    ## [[1]]

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](activity11-simulation_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

    ## 
    ## [[2]]
    ## # A tibble: 4 x 3
    ##   distribution      mean    sd
    ##   <chr>            <dbl> <dbl>
    ## 1 mean_binomial    25.0  0.392
    ## 2 mean_exponential  5.00 0.419
    ## 3 mean_normal      50.0  0.792
    ## 4 mean_uniform     15.0  0.247

How do these values compare to your work with `n = 30`?

### Challenge 1

Hopefully, this version of the CLT seems like a refresher. However, it
would be nice to see if this holds for other statistics? Instead of
calculating the mean of each of the 1,000 samples, explore one or a few
(or all) of the following summary statistics:

-   median
-   midhinge
-   maximum
-   standard deviation
-   IQR

## Challenge 2: Apply this knowledge

GVSU claims that the Laker Line bus route has a 10 minute wait time
during the peak hours of 7 am - 9 pm
(<https://www.gvsu.edu/bus/bus-route-ll-gvsu-laker-line-weekday-69.htm>).
Assume that wait times are Exponentially distributed with a mean of 10
minutes. For Exponentially distributed variables, remember that mean = 1
/ `rate`. What is the `rate` of our population distribution?

You think this might be a lie. In the last 30 days during peak hours,
you have waited for the bus an average of 15 minutes.

If the bus system is telling the truth, how unlucky have you been this
past month? You will make an argument both by direct simulation and
using the CLT. (*Hint for CLT, i.e., z-score, method: remember that the
sd is equal to the mean.*)

### Using simulation

To do this,

1.  Simulate 30 random values (a sample of 30 days) from the appropriate
    exponential distribution.
2.  Do (1), 1,000 times
3.  Calculate the mean of each of the 1,000 samples
4.  Calculate what percentage of the sample means waited as long or
    longer than you.

### Using CLT

To do this,

1.  Recall that the CLT states that all potential sample means are
    approximately normal with mean 10 (GVSU’s claim) and a standard
    deviation of the population standard deviation (you calculated this
    by-hand above) divided by the square root of the sample size (30
    days).
2.  Find the probability from the normal distribution in (1) that a
    sample mean is as long as or longer than your time of 15 minutes.
    You do not need to use any simulations, simply the appropriate
    `xnorm` function (where `x` is replaced with the appropriate one of
    either `d`, `p`, `q`, or `r`).

Comment on how similar/different your results were for the simulation
and CLT methods.

Do you believe that bus wait times are Exponentially distributed?

![](README-img/noun_pause.png) **Planned Pause Point**: If you have any
questions, contact your instructor. Otherwise feel free to continue on.

## Attribution

This activity is based on one of Kelly Bodwin’s [STA
331](https://www.kelly-bodwin.com/about/) labs.
