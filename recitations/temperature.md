---
layout: page
title: Forecasting Minimum Daily Temperature
permalink: /r/temperature/
menu: false
---

Melbourne, Australia: Minimum Daily Temperatures Dataset

In this assignment, you will explore the minimum daily temperatures for Melbourne, Australia. 

After data characterization and exploration, you will then develop a model to predict the minimum daily temperatures from a time period within the given data. While we can also try to predict outside of the given data, predicting inside the given years will allow us to evaluate our forecasting algorithm.

For this assignment you will likely work in either an Rmd ([R Markdown](http://rmarkdown.rstudio.com/)) file or an iPython notebook. If you choose to use a different environment (e.g., Julia), make sure that you commit everything needed to your class repository to run and understand what you've done.

## Data

This dataset contains the minimum daily temperatures in Celsius for Melbourne. It contains ten years of data from January 1st 1981 to December 31st 1990 with two columns (date and temperature) totaling of 3,650 observations. 

This data is provided by the [Australian Bureau of Meteorology](http://www.bom.gov.au/climate/data/) and more data can be downloaded from the website besides just minimum temperature data. For our purposes, we will download the data from [Machine Learning Mastery](https://machinelearningmastery.com/time-series-datasets-for-machine-learning/) to ensure we are all working from the same data. There is a download link under the "Minimum Daily Temperatures Dataset" header. 

## References

 * [R Language](http://www.r-project.org/) and [R Package System (CRAN)](http://cran.rstudio.com/)
 * [GGPlot2](http://ggplot2.org/)

## Instructions

Make a new directory in your class repository called temperature and put your work for this assignment (recitation) in there.

### Part 1: Preliminary Analysis ###

**Q1:** Taking a first look at the data we will first convince ourselves that the data looks correct by checking the beginning, end, and dimension of the data. 

We can run these commands to check this:

{% highlight r %}
head(data)
tail(data)
dim(data)
{% endhighlight %}

It should confirm the information given above. As a next step, it would be helpful to visualize the data. We can plot by changing the date column (read in as a factor) as needed or we can plot by the index using code like this:

{% highlight r %}
qplot(seq_along(data$Temp), data$Temp) + geom_line()
{% endhighlight %}

Try plotting the data both by using the index and date.

What do you notice from the plot? Are there any trends that you can see? If so, what are they?

**Q2:** As we begin to explore the data, we will want to look at the data from multiple possible periods such as months, years, seasons, and weekdays. We will want to add these columns to the dataframe to include in our analysis. Do so now. <br>

{% highlight r %}
agg <- aggregate(data$Temp,
                by = list(data$month, data$year),
                FUN = mean)
{% endhighlight %}

This above code will group aggregate by the month and year as well as calculating the mean for the temperature. Renaming the columns will also be helpful at this point to keep track of your new dataframes resulting in aggregating. One consideration is what function you would like to use to aggregate such as mean (shown above), min or max. Note your choice and explain why.

Once you've aggregated the data a few different ways, visualize each one. What do you notice from these plots? Is this helpful to see different trends that may not be apparent by plotting all of the data at once? If so, what do you notice that you didn't previously?

**Q3:** Autocorrelation and Partial Autocorrelation plots can be very useful when analyzing time series data. It can provide insight to the lag values that will be helpful in forecasting. This [website](https://newonlinecourses.science.psu.edu/stat510/lesson/2/2.2) provides some background and interpretation help for the Autocorrelation Function (ACF) and the Partial Autocorrelation Function (PACF). 

The documentation for each of these functions in R can be found here as well: <br>[acf](https://www.rdocumentation.org/packages/stats/versions/3.6.1/topics/plot.acf) <br>[pacf](https://www.rdocumentation.org/packages/tseries/versions/0.1-2/topics/pacf). 

After reading about these functions, what is the key difference between ACF and PACF?

**Q4:** Now we will actually implement ACF and PACF. Calling these functions is pretty simple using code like this:

{% highlight r %}
pacf(data$Temp)
{% endhighlight r %}

But the intrepretation of the graphs is the most important and more complicated part. Interpret the graphs. What information can you gather from the ACF plot? What about the PACF plot?

### Part 2: On your own now ###

Before we dig into the modeling portion, one key difference between time series data and other data is the autocorrelation. Due to this, creating a testing and training set will not be created through random sampling. Sampling randomly would allow information to leak into your testing set and provide invalid model results. You will want to split data based on portions of time and forecast for the next time step(s). This will make more sense as you start looking into Prophet next...

**Q5:** Fit and Predict Minimum Temperature values using [Prophet](https://facebook.github.io/prophet/docs/quick_start.html#r-api). 

**Q6:** Evaluate your Prophet model. 

### Extra Credit

**EC1:** Pick from ARIMA or LSTM to train a new forecasting model. Keep in mind that you will need to create a training and test set. It will be best if you can also do a time series cross validation which is different from a typical cross validation technique for the reason stated at the end of Question 4. 

Here is some more information about the [cross validation](https://towardsdatascience.com/time-series-nested-cross-validation-76adba623eb9) for time series data

Here is a resource for [ARIMA](https://datascienceplus.com/time-series-analysis-using-arima-model-in-r/)

Here is a resource for [LSTM](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/)

Include: 
- Data Characterization: exploratory, ACF, and PACF plots with interpretations and code
- Model: code, results, and findings
- Discussion: next steps and lessons learned
