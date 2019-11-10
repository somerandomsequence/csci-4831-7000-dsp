---
layout: page
title: Forecasting Minimum Daily Temperature
permalink: /r/temperature/
menu: false
---

Minimum Daily Temperatures Dataset

In this assignment, you will explore the minimum daily temperatures for a location of your choice. 

After data characterization and exploration, you will then develop a model to predict the minimum daily temperatures from a time period within the given data. While we can also try to predict outside of the given data, predicting inside the given years will allow us to evaluate our forecasting algorithm.

For this assignment you will likely work in either an Rmd ([R Markdown](http://rmarkdown.rstudio.com/)) file or an iPython notebook. If you choose to use a different environment (e.g., Julia), make sure that you commit everything needed to your class repository to run and understand what you've done.

## Data

You will want to visit [NOAA](https://www.ncdc.noaa.gov/cdo-web/search)'s website and fill out the fields for the Climate Data Online Search fields. You will want to select "Daily Summaries" for the "Select Weather Observation Type/Dataset" field. Find a city that you're interested in and continue working through the steps once you've added that city to your cart. Under the "Select data types for custom output" be sure to check the minimum temperature field under "Air Temperature."

It would be helpful to gather a good amount of data. I would suggest around 100 years of data.

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

### Part 2: On your own now: Modeling ###

Before we dig into the modeling portion, one key difference between time series data and other data is the autocorrelation. Due to this, creating a testing and training set will not be created through random sampling. Sampling randomly would allow information to leak into your testing set and provide invalid model results. You will want to split data based on portions of time and forecast for the next time step(s).

Throughout this tutorial on LSTM, we will be using pieces from this [tutorial](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/) by RStudio. There will be modifications, so be sure to follow along with the steps listed here and not the steps in the blog. If you'd like a deeper dive into LSTM, I would suggest reviewing this blog post in more detail.

Packages:<br>
Make sure you run install_keras() if you are working in R instead of the typical install.packages(). Also, bring in recipes, dplyr, tibble, tibbletime, and ggplot2.

**Q5:** Transforming the data for modeling:<br>
a. Create three columns: date as Date type, temperature as a numeric value, and a sequential id after sorted by Date ([seq.int](https://www.rdocumentation.org/packages/base/versions/3.6.1/topics/seq)) to help quickly verify set lengths throughout this process <br>
b. Split data into a training and validation set (we will address the test set later) by just using the indexes of the dataframe (About 2/3 of the data for training and 1/3 for testing is a place to start)<br>
c. Bind the training and validation set together with a key and set the index to be the date<br>

Assuming the_date is the Date type of dates, the code for part c may look something like this: 
{% highlight r %}
df <- bind_rows(
  df_trn %>% add_column(key = "training"),
  df_val %>% add_column(key = "validation"),
) %>%
  as_tbl_time(index = the_date)
{% endhighlight r %}
* code credit to this [RStudio](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/) tutorial.

d. Determine the timesteps and batch size<br>
	<p>1. Daily data: How far ahead do you want to predict? Try to catch a natural trend period (consider the ACF and PACF plots)</p><br>
	<p>2. Batch size: Where would you like to divide the data to predict?</p><br>
e. Center the data and store the values to undo tranformation later<br>

Assuming Temp is the temperatures and df is your dataframe with the key column added, the code for part e may look something like this: 
{% highlight r %}
rec_obj <- recipe(Temp ~ ., df) %>%
  step_sqrt(Temp) %>%
  step_center(Temp) %>%
  step_scale(Temp) %>%
  prep()
  
center_history <- rec_obj$steps[[2]]$means["Temp"]
scale_history <- rec_obj$steps[[3]]$means["Temp"]
{% endhighlight r %}
* code credit to this [RStudio](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/) tutorial.

**Q6:** Modeling:
f. Build the data for modeling:<br>
	<p>1. Define functions: build matrix and reshape X (given in the blog)</p><br>
	<p>2. Extract values, build, create, and reshape matrix</p>

Assuming df_processed_tbl is the result from the bake() with the rec_obj and df, the code for part f2 for the training set may look something like this: 
{% highlight r %}
train_vals <- df_processed_tbl %>%
  filter(key == "training") %>%
  select(Temp) %>%
  pull()

train_matrix <- 
  build_matrix(train_vals, n_timesteps + n_predictions)
  
X_train <- train_matrix[, 1:n_timesteps]
y_train <- train_matrix[, (n_timesteps+1):(n_timesteps * 2)]

X_train <- reshape_X_3d(X_train)
y_train <- reshape_X_3d(y_train)
{% endhighlight r %}
* code credit to this [RStudio](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/) tutorial.

g. initialize flags<br>
h. itialize number of predictions, number of features, callbacks<br>
i. create model, add layers, fit the model, plot the history<br>

Assuming , the code for part i (fit the model) may look something like this: 
{% highlight r %}
history <- model %>% fit(
  x           = X_train,
  y           = y_train,
  validation_data = list(X_valid, y_valid),
  batch_size  = FLAG$batch_size,
  epochs      = FLAG$in_epochs,
  callbacks   = callbacks
)
{% endhighlight r %}
* code credit to this [RStudio](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/) tutorial.


### Extra Credit

**EC1:** Evaluating your model. <br>
j. Predict within training set (remember to transform back)<br>
k. Plot actual vs predicted values<br>
l. Tune parameters, repeat g-k until satisfied with results<br>
m. What is the final model you have chosen? Why?<br>

**EC2:** Pick from ARIMA or Prophet to train a new forecasting model. Keep in mind that you will need to create a training and test set. It will be best if you can also do a time series cross validation which is different from a typical cross validation technique for the reason stated at the end of Question 4. 

Here is some more information about the [cross validation](https://towardsdatascience.com/time-series-nested-cross-validation-76adba623eb9) for time series data

Here is a resource for [ARIMA](https://datascienceplus.com/time-series-analysis-using-arima-model-in-r/)

Here is a resource for [Prophet](https://facebook.github.io/prophet/docs/quick_start.html#r-api)

Include: 
- Data Characterization: exploratory, ACF, and PACF plots with interpretations and code
- Model: code, results, and findings
- Discussion: next steps and lessons learned
