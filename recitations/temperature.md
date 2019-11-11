---
layout: page
title: Forecasting Temperature
permalink: /r/temperature/
menu: false
---

In this assignment, you will explore the daily temperatures for a location of your choice. 

After data characterization and exploration, you will then develop a model to predict the daily temperatures from a time period within the given data. While we can also try to predict outside of the given data, predicting inside the given years will allow us to evaluate our forecasting algorithm.

For this assignment you will likely work in either an Rmd ([R Markdown](http://rmarkdown.rstudio.com/)) file or an iPython notebook. If you choose to use a different environment (e.g., Julia), make sure that you commit everything needed to your class repository to run and understand what you've done.

## Data

You will want to visit [NOAA](https://www.ncdc.noaa.gov/cdo-web/search)'s website and fill out the fields for the Climate Data Online Search fields. You will want to select "Daily Summaries" for the "Select Weather Observation Type/Dataset" field. Find a city that you're interested in and continue working through the steps once you've added that city to your cart. Under the "Select data types for custom output" be sure to check the minimum temperature field under "Air Temperature." You can also work to predict maximum temperature if you prefer (or the difference between the two).

It would be helpful to gather a good amount of data. I would suggest around 100 years of data.

## References

 * [R Language](http://www.r-project.org/) and [R Package System (CRAN)](http://cran.rstudio.com/)
 * [GGPlot2](http://ggplot2.org/)
 * [RStudio tutorial](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/)
 * [Keras Documentation](https://keras.rstudio.com/index.html)
 * [ARIMA](https://datascienceplus.com/time-series-analysis-using-arima-model-in-r/)
 * [Prophet](https://facebook.github.io/prophet/docs/quick_start.html#r-api)
 * [cross validation](https://towardsdatascience.com/time-series-nested-cross-validation-76adba623eb9) 


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

Note that in this example, the Temp variable corresponds to the daily minimum temperture. You can also plot the maximum and the daily temperture range if you prefer.

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

The documentation for each of these functions in R can be found here as well: <br>[acf](https://www.rdocumentation.org/packages/stats/versions/3.6.1/topics/plot.acf) <br>[pacf](https://www.rdocumentation.org/packages/tseries/versions/0.1-2/topics/pacf)

After reading about these functions, what is the key difference between ACF and PACF?

**Q4:** Now we will actually implement ACF and PACF. Calling these functions is pretty simple using code like this:

{% highlight r %}
pacf(data$Temp)
{% endhighlight r %}

But the intrepretation of the graphs is the most important and more complicated part. Interpret the graphs. What information can you gather from the ACF plot? What about the PACF plot?

### Part 2: Modeling with LSTM

Before we dig into the modeling portion, one key difference between time series data and other data is the autocorrelation. Due to this, creating a testing and training set will not be created through random sampling. Sampling randomly would allow information to leak into your testing set and provide invalid model results. You will want to split data based on portions of time and forecast for the next time step(s).

For the modeling piece of this recitation, we will be exploring a Long-short term memory (LSTM) model. An LSTM is a Recurrent Neural Network (RNN) and considers the past observations. This is particularly helpful in scenarios where the future is determined in part by the past. Time series data is one application for LSTMs. We will be exploring Keras within this recitation, which is a part of Tensorflow.

Throughout this tutorial on LSTM, we will be using pieces from this [tutorial](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/) by RStudio. There will be modifications, so be sure to follow along with the steps listed here and not the steps in the blog. If you'd like a deeper dive into LSTM, I would suggest reviewing this blog post in more detail.

**Packages:**

Make sure you run install_keras() if you are working in R instead of the typical install.packages(). This will allow for the keras package as well as the Tensorflow backend to be installed. You can find more information about the installation [here] (https://keras.rstudio.com/index.html). 

Also, install the packages: recipes, dplyr, tibble, tibbletime, and ggplot2.

**Transforming the data for modeling:**

We will need to do some additional data transforming before training a LSTM model.

**a.** We will want to make sure we have a dataframe created with these three columns: date as Date type, temperature as a numeric value, and a sequential id after sorted by Date ([seq.int](https://www.rdocumentation.org/packages/base/versions/3.6.1/topics/seq)) to help quickly verify set lengths throughout this process <br>

**b.** Split data into a training and validation set (we will address the test set later) by just using the indexes of the dataframe. Using about 2/3 of the data for training and 1/3 for testing is a reasonable place to start.<br>

**c.** Bind the training and validation set together with a key and set the index to be the date<br>

Assuming the_date is the Date type of dates, the code for part c may look something like this: 
{% highlight r %}
df <- bind_rows(
  df_trn %>% add_column(key = "training"),
  df_val %>% add_column(key = "validation"),
) %>%
  as_tbl_time(index = the_date)
{% endhighlight r %}

(code credit to this [RStudio tutorial](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/))

**d.** Determine the timesteps and batch size through answering the following two questions and initializing the variables n_timesteps, n_predictions, and batch_size.<br>
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. Daily data: How far ahead do you want to predict? Try to catch a natural trend period (consider the ACF and PACF plots)<br>
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. Batch size: Where would you like to divide the data to predict?<br>

**e.** Next you'll want to center the data and store the values to undo tranformation later.<br>

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

(code credit to this [RStudio tutorial](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/))

**Modeling:**

**f.** Next, you'll need to build the data for modeling:<br>

1. Define functions: build matrix and reshape X (given in the tutorial)<br>
1. Extract values, build, create, and reshape matrix

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

(code credit to this [RStudio tutorial](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/))

**g.** Now you'll want to initialize the flags which will contain a lot of information for the LSTM model and parameters that you may want to consider changing or tuning. There is an example of this in the tutorial as well.<br>

**h.** Now initialize the number of predictions, number of features, callbacks, and optimizer. Initialize from the flags instead of manually as we did in Q5 when possible. You may also look into the optimizer more, but we won't cover that in this tutorial.<br>

**i.** Next, you'll create the model, add layers, fit the model, and plot the history.<br>

The code for part i (fit the model) may look something like this: 
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

(code credit to this [RStudio tutorial](https://blogs.rstudio.com/tensorflow/posts/2018-06-25-sunspots-lstm/))

In this process, you should see the model being fit on the training data and repeatedly assessed against the validation data.

### Extra Credit

**EC1:** Tuning your model: <br>
**j.** Predict for a time window within training set and remember to transform back using center_history and scale_history, which we stored in Q5.<br>
**k.** Plot actual versus predicted values against time.<br>
**l.** Tune your parameters by repeating g-k until your satisfied with results.<br>
**m.** What are the parameters you've chosen? Why? Any other observations?<br>

**EC2:** Creating more robust results:<br>
**n.** Add a testing set in the above workflow.<br>
**o.** Use the testing set to evaluate the model.<br>
**p.** Add backtesting and get the RMSE results for both the testing and training with 6 splits.<br>

**EC3:** Pick from ARIMA or Prophet to train a new forecasting model. Keep in mind that you will need to create a training and test set. It will be best if you can also do a time series cross validation which is different from a typical cross validation technique due to sequential aspect of the data.

Helpful resources:
 * [cross validation](https://towardsdatascience.com/time-series-nested-cross-validation-76adba623eb9) for time series data
 * [ARIMA](https://datascienceplus.com/time-series-analysis-using-arima-model-in-r/)
 * [Prophet](https://facebook.github.io/prophet/docs/quick_start.html#r-api)

Include: 
- Data Characterization: exploratory, ACF, and PACF plots with interpretations and code
- Model: code, results, and findings
- Discussion: next steps and lessons learned
