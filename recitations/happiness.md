---
layout: page
title: Understanding Happiness
permalink: /r/happiness/
menu: false
---

In this assignment you will explore happiness in the US using the [General Social Survey](http://gss.norc.org/), a database of survey results conducted by [NORC at the University of Chicago](http://www.norc.org/) from 1972 to present.

The main research question here is: what makes people happy? Or, more specifically, what activities or life choices are most clearly correlated with self-reported happiness. And, over a long period of time, how have these trends changed.

For this assignment you will likely work in either an Rmd ([R Markdown](http://rmarkdown.rstudio.com/)) file or an iPython notebook. If you choose to use a different environment (e.g., Julia), make sure that you commit everything needed to your class repository to run and understand what you've done.

## Data

Download the data: [gss.csv.bz2](https://FIXME) (20 MB) 
And the codebook: [gss-codebook.pdf](https://FIXME) (36 MB)

To save you a bit of time, this data has already been converted from the proprietary SPSS format to CSV. Once extracted, the data is about 800 MB in a single CSV. You don't need to extract the file unless you want to, since many CSV readers (including those in R and Pandas' read\_csv function) can read a bzipped CSV directly.

This data contains 64,814 rows (surveys) and many columns. The columns are described in detail in the GSS codebook, which you'll need as well. Start by familiarizing yourself with the GSS codebook and how it is organized. You'll see that the columns (questions) are organized into modules and each year a different set of modules are asked of the survey participants. This means the data may be somewhat *sparse*, i.e., there are likely a number of empty values in some columns since every question isn't asked every year. Page 12 has a complete list of columns with brief descriptions.

## References

 * [R Language](http://www.r-project.org/) and [R Package System (CRAN)](http://cran.rstudio.com/)
 * [GGPlot2](http://ggplot2.org/) and [GGMap](http://cran.r-project.org/web/packages/ggmap/ggmap.pdf)

## Instructions

### Part 1: Training Wheels ###

To get you started, we'll walk through some basic data processing and analysis with R. You don't need to use R to complete this assignment, but it's certainly a good candidate.

Your task is to perform some statistical analyses to look at correlations between happiness and other variables, and then to fit a model using some of these variables.

First, let's read in the first 1000 rows of the data to have a look at it:

{% highlight r %}
df <- read.csv('gss.csv.bz2',nrows = 1000)
colnames(df)
{% endhighlight %}

There are a few questions (columns) that relate to the respondant's happiness.  Let's take a look at those:

{% highlight r %}
summary(df$HAPPY7)
summary(df$HAPPY)
summary(df$HAPUNHAP)
{% endhighlight %}  

According to the data and the code book, HAPPY is the only one with good coverage over the duration of data collection, so let's focus on that one. HAPPY is coded as:

  * Very Happy = 1
  * Pretty Happy = 2
  * Not too happy = 3
  * Don't know = 8
  * No answer = 9
  * Not applicable = 0

Let's tell R that this data is categorical (a 'factor' in the R nomenclature):

{% highlight r %}
df$HAPPY <- factor(df$HAPPY,levels=c(1,2,3,8,9,0),labels=c("Very happy","Pretty happy","Not too happy","Don't know","No answer","Not applicable"))
summary(df$HAPPY)
{% endhighlight r %}

Looks like most people are pretty happy. Let's suppose (hypothesize) that people who are healthier also may be happier. There's also a variable called HEALTH with this coding:

  * Excellent = 1
  * Good = 2
  * Fair = 3
  * Poor = 4
  * Don't know = 8
  * No answer = 9
  * Not applicable = 0

After converting the health column to a factor, if we want to look at how happyness and health are correlated, we could try a contingency table. To simplify things, let's also create a new dataframe (df2) where we omit those who didn't provide an answer:

{% highlight r %}
df2 <- subset(df,as.numeric(df$HEALTH) %in% c(1,2,3,4) & as.numeric(df$HAPPY) %in% c(1,2,3))
df2$HAPPY <- factor(df2$HAPPY)
df2$HEALTH <- factor(df2$HEALTH)
thh <- with(df2,table(HEALTH,HAPPY))
plot(thh)
thh
{% endhighlight r %}

**Q1:** Take a look at this contingency table. From a first look, does it appear that there's any relationship between the variables? Are healthy people more likely to be happy or unhappy? How about unhealthy people?

Using a [Chi-squared test](https://en.wikipedia.org/wiki/Chi-squared_test), we can measure how likely it is that these data are independent (i.e., that there is no correlation or dependency between them).

**Q2:** Try performin a chi-square test on these two factors. Are they independent or does it appear that happiness and health are interrelated? Why?

Now let's try a different variable, that's numeric not categorical, like the age of the participants. We can make a box and whiskers plot using the Grammar of Graphics (GGPlot) to compare the distribution of age in each happiness category

{% highlight r %}
library(ggplot2)
ggplot(df) + geom_boxplot(aes(x=HAPPY,y=AGE))
{% endhighlight %}

We can use an [Analysis of Variance (ANOVA)](https://en.wikipedia.org/wiki/Analysis_of_variance) to test whether there's a significant difference in the mean age between each happiness group. To do this in R, you'd use the following code:

{% highlight r %}
summary(aov(AGE ~ HAPPY,data=df))
{% endhighlight %}

**Q3:** Does age explain happyness according to the ANOVA? Why or why not?

Now *load the full data* instead of just the first 1,000 rows.

Now let's try to look at long term trends grouping respondants into categories by year. In R, you'd do this with the aggregate function. The first example below calculates the mean happiness or health. The second calculates the percentage of people who are very happy or in excellent health. The latter approach is better for this data, since it's categorical.

{% highlight r %}
mean.by.year <- aggregate(cbind(HEALTH,HAPPY) ~ YEAR,data=df,mean)

pct.less.than.two <- function(x){ 100*sum(as.numeric(x) < 2)/length(x) }
pct.by.year <- aggregate(cbind(HEALTH,HAPPY) ~ YEAR,data=df,FUN=pct.less.than.two)
{% endhighlight %}

You can make a scatterplot of these data using ggplot or another tool. You might want to plot a single variable over time, or plot health and happyness against each other.

{% highlight r %}
ggplot(pct.by.year) + geom_point(aes(x=YEAR,y=HAPPY))
ggplot(pct.by.year) + geom_point(aes(x=HEALTH,y=HAPPY))
ggplot(pct.by.year,aes(x=YEAR,y=HAPPY)) + geom_point() + geom_smooth(method="lm")
{% endhighlight %}

**Q4:** To understand how well these variables are correlated, you can use [Pearson's correlation](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient) and/or fit a linear regression. Calculate the correlation coefficient and p-value for each combination of variable (health and year, happiness and year, health and happyness). In R, the function is called 'cor.test'. Are they correlated? How well? What does this mean?

**Q5:** Next, fit a regression model with HAPPY as the dependent variable, and YEAR and HEALTH as the independent variables (features). How good is this regression model? What does it mean?

#### Part 2: On your own now ####

For the next part of this assignment, you'll go out on your own:


**Q6:** Define three hypotheses about additional variables that may be correlated with happiness. You'll need to reference the code book. Why do you think these may or may not be correlated with happiness?

**Q7:** Using methods like those in part 1, explore whether or not the data supports your hypotheses.

**Q8:** Using a binary classification model (like logistic regression) combine all these factors to make one synthesized model (including age and health from above as well). Since logistic regression is a binary classification model, you'll need to reduce the HAPPY variable to 2 levels (e.g., HAPPY = 1 or 2 and HAPPY = 3). Explain your decision here and how it may impact the results. What does the model tell you about happiness and what is correlated with it?

#### Extra Credit

**EC1:** Finally, try a multi-class classification model like a random forest, neural network, or support vector machine. How well or poorly does this work? Which variables contribute most to your model (i.e., can you explain what contributes most to happiness using this model?).