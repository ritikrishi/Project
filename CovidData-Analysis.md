Inspecting COVID-19 Data
================
Ritik
2023-05-18

## COVID-19 Case Study

After completing my “Google Analytics Professional Certification” and
“Business Analytics for Management Decision from NPTEL” I decided to
utilize R and stastics in order to analyze various datasets.

I used this [page on
Kaggle](https://www.kaggle.com/datasets/sudalairajkumar/novel-corona-virus-2019-dataset/versions/25?resource=download)
and found a CSV dataset.

 
![COVID19 DATA](/../ritikrishi-patch-1/image_covid_data.png)

Here we have data for about 1,000 cases of COVID-19. We can see the age
of all the infected person, the gender, whether they recovered or died,
among many other things.

Let us import the data. In RStudio, to import the dataset, we will go to
File-\>Import Dataset-\>From Text(base). Find your CSV file and click
“Import.”

``` r
COVID19_line_list_data <- read.csv("~/Downloads/archive/COVID19_line_list_data.csv")
```

For convenience I renamed the data frame variable to “cov_data.” I also
cleared all existing variables, import a library called Hmisc, and use
its describe function to better understand our data.

``` r
rm(list = ls()) # remove all variables stored previously
library("Hmisc")  
```

    ## 
    ## Attaching package: 'Hmisc'

    ## The following objects are masked from 'package:base':
    ## 
    ##     format.pval, units

``` r
cov_data <- read.csv("~/Downloads/archive/COVID19_line_list_data.csv")
describe(cov_data) 
```

“describe(cov\_”Hmisc”)” If you run this code, you will see a lot of
information in the console. For instance, we have 1085 rows. If we look
at the death section, we see that there are 14 distinct values. This may
seem a little bit weird, but the death column either has a 0 (no death),
1 (no death), or simply the date of the patient’s death. This is
difficult to work with because we want all 0s and 1s.

Let us fix this by adding a death_dummy column to our dataset, which
only contains the values 0 and 1.

#### Clean up death column

``` r
cov_data$death_dummy <- as.integer(cov_data$death != 0)

sum(cov_data$death_dummy) / nrow(cov_data)
```

    ## [1] 0.05806452

We also calculated the death rate of our dataset, which after running
turns out to be 5.8%.

## AGE

According to the media, elderly persons have a higher mortality rate
from COVID-19 than younger people. Is this a fact? Let’s examine our
dataset first. In order to compare the mean ages, we will first divide
our dataset into patients who are still alive and patients who have
passed away. We can accomplish it with this code:

``` r
# AGE
cov_data <- read.csv("~/Downloads/archive/COVID19_line_list_data.csv")
cov_data$death_dummy <- as.integer(cov_data$death != 0)
dead <- subset(cov_data, death_dummy == 1)
alive <- subset(cov_data, death_dummy == 0)
mean(dead$age, na.rm = TRUE)
```

    ## [1] 68.58621

``` r
mean(alive$age, na.rm = TRUE)
```

    ## [1] 48.07229

The na.rm=TRUE specifies that rows should be skipped if the specified
column (in this case, age) has NA (or no value). Running this gives us
the results that the average age of those who survived is 48 years,
whereas the average age of those who died is approximately 68.6 years.

Okay, so the data do really indicate that the older our sample’s death
rate is. But does this apply to everyone in society? In what degree do
we believe this to be true?

To determine our level of confidence and whether we can trust our means,
we can utilise the t.test command. We’ll use a 95% confidence interval
in this instance.

``` r
t.test(dead$age, alive$age, alternative="two.sided", conf.level = 0.95)
```

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  dead$age and alive$age
    ## t = 10.839, df = 72.234, p-value < 2.2e-16
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  16.74114 24.28669
    ## sample estimates:
    ## mean of x mean of y 
    ##  68.58621  48.07229

With 95% certainty, based on the confidence interval, the age difference
between patients who have died and those who have not is between 16.7
and 24.3 years. Look at the p-value right now. It’s almost zero. This
indicates that under the null hypothesis (that the ages of the two
groups are equal), there is 0% chance that we would have randomly
achieved such an extreme result from this sample. Because of this, we
can reasonably rule out the null hypothesis (under the accepted
significance threshold of 0.05) and assert that COVID-19 fatalities are
older than non-fatalities. Let’s now examine gender!

## GENDER

We want to see if the death rate is similar for men and women. Let us
again split our data and perform the t-test:

``` r
# GENDER
men = subset(cov_data, gender == "male")
women = subset(cov_data, gender == "female")
mean(men$death_dummy, na.rm=TRUE)
```

    ## [1] 0.08461538

``` r
mean(women$death_dummy, na.rm=TRUE)
```

    ## [1] 0.03664921

``` r
t.test(men$death_dummy, women$death_dummy, alternative="two.sided", conf.level = 0.99)
```

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  men$death_dummy and women$death_dummy
    ## t = 3.084, df = 894.06, p-value = 0.002105
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 99 percent confidence interval:
    ##  0.007817675 0.088114665
    ## sample estimates:
    ##  mean of x  mean of y 
    ## 0.08461538 0.03664921

Our 99% confidence interval indicates that the mean will have a higher
death rate than women of 1.7% to 7.8%. Given that 0.002 \< 0.05, the
null hypothesis can be rejected.

## CONCLUSION

In this COVID-19 data analysis research, we looked at how age, gender,
and death rate related to one another. With an average age of 68.6 years
for those who passed away compared to 48 years for survivors, the
findings demonstrated that older people had a greater death rate. Men
also died at a rate that was higher than that of women. These findings
show that COVID-19 mortality varies by gender and by age. title:
“Untitled” author: “Ritik” date: “2023-05-18” output: html_document —

