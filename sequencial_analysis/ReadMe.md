I'm planning to practice more on time series analysis, pattern matching and sequential pattern matching.


******************************************************************************************

TIME SERIES CONCEPTS

* For previous summarized knowledge, check [-- Time Series section][1]
* ARIMA is an acronym that stands for <b>AutoRegressive Integrated Moving Average</b>. It is a class of model that captures a suite of <b>different standard temporal structures</b> in time series data.
* AR: Autoregression. A model that uses the dependent relationship between an observation and some number of lagged observations.
* I: Integrated. The use of differencing of raw observations (e.g. subtracting an observation from an observation at the previous time step) in order to make the time series stationary.
* MA: Moving Average. A model that uses the dependency between an observation and a residual error from a moving average model applied to lagged observations.
* Before building the model, there is data preprocessing necessary to make the data stationary. How to check Stationary?
    * First of all, what makes data non-stationary
      * <b>Trend</b>: Varying mean over the time. If you see the data is increasing along the time, off course the mean is increasing too
      * <b>Seasonality</b>: Variance at specific time frame. For example, sales of ice-cream in summer is much more than winter.
    * Why need stationary data
      * I guess it's because, after removing the trend and seasonality, it is easier to make forecast. After the forecast, you can add the trend and seasonality back
    * Check Stationary - Plotting Rolling Statistics
      * Check <b>moving average</b> or <b>moving variance</b>, to see whether they are changing alog the time
    * Check Stationary - Dickey-Fuller Test
      * It has null hypothesis that time series is non-stationary. If <b>Test Statistic is less than Critical Values</b>, then reject the null hypothesis and the data is statinoary.
  * Make time series stationary
    * It's almost impossible to make it perfectly stationary, but we can try to make it closer to be stationary
    * By doing this data preprocessing before forcasting, we remove the trend, seasonality in time series and make it stationary, forcasting can be done on this stationary data, later we can convert forecast values back to the original scale, by adding the trend and seasonality back
    * Mehtods to find trend in order to remove it from time series
      * Aggregation – taking average for a time period like monthly/weekly averages
      * Smoothing – taking rolling averages
      * Polynomial Fitting – fit a regression model
  * For ARIMA, we can follow <b>Box-Jenkins Methodology</b>
    * <b>Model Identification</b>. Use plots and summary statistics to identify trends, seasonality, and autoregression elements to get an idea of the amount of differencing and the size of the lag that will be required.
    * <b>Parameter Estimation</b>. Use a fitting procedure to find the coefficients of the regression model.
    * <b>Model Checking</b>. Use plots and statistical tests of the residual errors to determine the amount and type of temporal structure not captured by the model.
* After data preprocesing for stationary, there could be 2 results:
  * A strictly stationary series with no dependence among the values. This is the easy case wherein we can model the residuals as white noise. But this is very rare.
  * A series with significant dependence among values. In this case we need to use some statistical models like ARIMA to forecast the data.
  * <b>Autocorrelation Function (ACF)</b>: It is a measure of the correlation between the time series with a lagged version of itself. For instance at lag 5, ACF would compare series at time instant ‘t1’…’t2’ with series at instant ‘t1-5’…’t2-5’ (t1-5 and t2 being end points).
  * <b>Partial Autocorrelation Function (PACF)</b>: This measures the correlation between the TS with a lagged version of itself but after eliminating the variations already explained by the intervening comparisons. Eg at lag 5, it will check the correlation but remove the effects already explained by lags 1 to 4.
* <b>Params (p,d,q)</b> are used to determine which specific ARIMA model will be used
  * <b>p</b>: The number of lag observations included in the model, also called the lag order. <b>It is The lag value where the PACF chart crosses the upper confidence interval for the first time</b>
  * <b>d</b>: The number of times that the raw observations are differenced, also called the degree of differencing.
  * <b>q</b>: The size of the moving average window, also called the order of moving average.
  * When set 0 for a param here, means not using this param. Ans therefore, we can simply use AR, I or MA model
* A linear regression model is constructed including the specified number and type of terms, and the data is prepared by a degree of differencing in order to make it stationary, i.e. to remove trend and seasonal structures that negatively affect the regression model.
* Adopting an ARIMA model for a time series assumes that the underlying process that generated the observations is an ARIMA process.
* [VISUALIZED AR, MA example in ACF, PACF][2], this in fact is a good example. Use ACF to find MA model, use PACF to find AR model
  * [reference][3]
    * It also includes <b>parameter estimation</b>, `fit <- arima(data,order=c(p,d,q))`
    * <b>Diagnostic Checking</b>, to check non-randomness with residuals, `tsdiag(fit)`. The residuals of a “correctly specified” model should be independently distributed, otherwise it's the wrong model
    * <b>Predict Future Values of Time Series</b>, `LH.pred<-predict(fit,n.ahead=8)`
 

******************************************************************************************
 
TIME SERIES PRACTICE

* [R] [Previous practice][6]
  * [reference][7]
 
* [R] Time series beginner
  * [Watch the free videos here FIRST!][8]
    * Often time series are generated as `Xt=(1+pt)Xt−1`, meaning the value of the time series observed at time t equals to the value observed at time t-1, and a small percent change pt at time t. pt is often refered to as <b>return/growth rate</b> of time series, and <b>it should be stable</b>. There is also `Yt=logXt−logXt−1≈pt`. In R, pt is calculated as `plot(diff(log(x)))`
    * <b>Stationary</b>: The <b>mean</b> is constant over time; The <b>correlation structure</b> remains constant over time
    * To convert non-stationary to stationary: Simple differencing can help remove the trend (detrend); You can also use logging + differencing, first logging will tablize the variance, then differencing will do detrend. In R, differencing is using `diff()`, logging is using `log()`. <b>To sum up, logging against Heteroscedasticity, differencing against the trend of the mean</b>.
    * It has been proved that, any stationary time series can be written as a linear combination of white noise, so do ARMA model. Therefore, in R, when you are simulating the model, it uses a list with (p, d, q)
  * [Reference][5]
    * This tutorial is messy, but if you go through it very carefully, there are still many things to learn
  * [My practice code when following the tutorial][4], it's messy too, I use it to try all the methods in the tutorial
  * [My summarized code][9], summarizing methods maybe re-used more often in the future
    * [About AR(0), AR(1) and AR(2)][11]
    * In the definition of AR(p) model [here][10]: the white noise has 0 mean and constant variance
    * As spectral analysis visualization showing, AR model will gradually decrease. MA model will suddenly decrease.
    * What is the role of ARMA, compared with AR, MA?
  * Notes:
    * Different from [my previous practice][6], which was using `ariam` to fit, this is using `sariam` for both fit and forecast. `sariam` is an improvement from `ariam`
    
[Python] Time Series 7 Methods
  * My code: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/time_series_forecasting.ipynb
    * `Holt_Winter` methods works the best, as it takes into account the level data, trend data and seasonal data
    * `Seasonal ARIMA` is an improvement of ARIMA, you need to set the season, you also need to know it's which ARIMA model it is in order to set (p,d,q) in `order()`
    * As you can see, the data used for forecasting is not stationary, but you can still do the forecast
    * How to install the library here
      * If you have laready had `statsmodels`, uninstall it through `pip uninstall statsmodels`
      * `git init`
      * `git clone git://github.com/statsmodels/statsmodels.git`
      * `python setup.py install`
      * `python setup.py build_ext --inplace`
      * Then check whether you can import the library, `from statsmodels.tsa.api import ExponentialSmoothing`
  * reference: https://www.analyticsvidhya.com/blog/2018/02/time-series-forecasting-methods/?utm_source=feedburner&utm_medium=email&utm_campaign=Feed%3A+AnalyticsVidhya+%28Analytics+Vidhya%29
    
* [Python] ARIMA Beginner
  * Python ARIMA Results library: http://www.statsmodels.org/devel/generated/statsmodels.tsa.arima_model.ARIMAResults.html
  * download dataset: https://datamarket.com/data/set/22r0/sales-of-shampoo-over-a-three-year-period#!ds=22r0&display=line
  * My code: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/python_ARIMA.ipynb
  * Better code: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/try_LSTM_Experiment1.ipynb
    * In Checking Stationary stage, used
      * Plotting Rolling Statistics: Compare Test Statistic with Critical Value (1%, 5%, 10%). For example, is Test Statistic is lower than 10% Critical Value, means there is 90% confidence that current data is stationary. Note! When you are checking the visualization, pay attention to y-axis, when it's larger, rolling mean and rolling std lines look flat but in fact may not be better than another graph with smaller y values. So, be careful when compare 2 graphs
      * Dickey-Fuller Test: Check RSS, if you are seeing nan, check whether there is nan or negative value and remove them, before using `np.sqrt`
    * In <b>Making It Stationary stage</b>, used smothooing methods
      * Moving Average - take average of ‘k’ consecutive values depending on the frequency of time series. Drawback is, you have to strictly define time period (such as taking yearly average). <b>[May not be good for data with strong Seasonality]</b>
      * Weighted Moving Average - more recent values are given a higher weight. <b>[May not be good for data with strong Seasonality]</b>
      * Differencing – taking the differece with a particular time lag [Eliminating Trend and Seasonality]
    * After ARIMA forecasting, it also added vack the original sacle, to compare with real observations
  * Check Stationary & Make Data Stationary Reference: https://www.analyticsvidhya.com/blog/2016/02/time-series-forecasting-codes-python/
  * ARIMA Reference: https://machinelearningmastery.com/arima-for-time-series-forecasting-with-python/
  
* [Python] RNN - Data Preprocessing for LSTM
  * This is another dataset, the data preprocessing to make data stationary here is better
  * My code: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/try_LSTM_Experiment1.ipynb
  
* [R] Stock Prediction - Part 1
  * Dataset Search Library - Quandl: https://www.quandl.com/search?query=
    * Through the website, create an account, then you can search and get access to the data
    * R library: https://cran.r-project.org/web/packages/Quandl/Quandl.pdf
    * US Stock: https://www.quandl.com/product/WIKIP/WIKI/PRICES-Quandl-End-Of-Day-Stocks-Info
      * Just type in US bank names and find results under Wiki EOD Stocks Info
      * Example: https://www.quandl.com/product/WIKIP/WIKI/PRICES-Quandl-End-Of-Day-Stocks-Info
  * My code: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/stock_analysis_part1.R
    * First of all, to generate GIF in R, you need `ImageMagick`. To install, check [Create Animated Visualization with R][14], then type `xclock &`. After you are seeing XQuartz is running, then turn on R studio
    * Acording to the code here, didn't have too much excited patterns. The most useful part is, data downloading through Quandl, it's a good data resource
  * reference: https://www.analyticsvidhya.com/blog/2017/09/comparative-stock-analysis/?utm_source=feedburner&utm_medium=email&utm_campaign=Feed%3A+AnalyticsVidhya+%28Analytics+Vidhya%29
  
* [R] Stock Prediction - Part 2
  * <b>Bollinger Bands</b> - The Bands depict the volatility of stock as it increases or decreases. The bands are placed above and below the moving average line of the stocks. <b>The wider the gap between the bands, higher is the degree of volatility.</b>
    * The <b>middle line</b> with N-period moving average (MA); 20-day SMA
    * An <b>upper band</b> at K times an N-period standard deviation above the moving average; 20-day SMA + (20-day standard deviation of price x 2)
    * A <b>lower band</b> at K times an N-period standard deviation below the moving average; 20-day SMA – (20-day standard deviation of price x 2)
    * SMA is Simple Moving Average, Standard Deviation, K and N period is usually set at 20 days. The upper and lower band are placed 2 units above and below respectively.
    * No wonder those people from economic background often check standard deviation with mean....
    ![Bollinger Bands](https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/09/28152914/Bollinger_Band.png)
  * How to read Bollinger Bands
    * If an uptrend is strong, then stock touches the upper band on regular basis and remains above the middle line.This signals the strong movement towards North.
    * If downtrend is strong, then stong touches the lower band on regular basis and remains below the middle line. This signals strong southward movement.
    * During the upward trend the price should not be below the lower band otherwise it is signalling the reverse movement.
    * During the downward trend, if the price move above the upper band then it signals Northward movement.
  * Signals to identify stock behaviours
    * <b>W-Bottom</b>: forms in a downtrend and involves two reaction lows (looks like W), and the second low is lower than the first low but holds above the lower band
    * <b>M-Tops</b>: forms in upper trens and has 2 highs. The first high can be higher or lower than the second high, but does't above the upper band
  * My code: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/stock_prediction_part2.R
    * This is the easiest, most good looking stock prediction visualization I have found
    * It uses the combination of regression and ARIMA to do the prediction, predict Close price on the end of the month. This method can be used for other time series prediction
  * reference: https://www.analyticsvidhya.com/blog/2017/10/comparative-stock-market-analysis-in-r-using-quandl-tidyverse-part-i/?utm_source=feedburner&utm_medium=email&utm_campaign=Feed%3A+AnalyticsVidhya+%28Analytics+Vidhya%29
  
  
******************************************************************************************

RNN - LSTM

* <b>LSTM</b>: The Long Short-Term Memory network, or LSTM network, is a recurrent neural network that is trained using Backpropagation Through Time and overcomes the vanishing gradient problem.
  * Instead of neurons, LSTM networks have <b>memory blocks</b> that are connected through layers.
  * A block operates upon an input sequence and each gate within a block uses the sigmoid activation units to control whether they are triggered or not, making the change of state and addition of information flowing through the block conditional.
  * 3 gates within a unit:
    * Forget Gate: conditionally decides what information to throw away from the block.
    * Input Gate: conditionally decides which values from the input to update the memory state.
    * Output Gate: conditionally decides what to output based on input and the memory of the block.
    * The gates of the units have weights that are learned during the training procedure.
* The benefit of LSTM is that, <b>it can learn and remember over long sequences and does not rely on a pre-specified window lagged observation as input</b>. What does this really mean in practice?
  * In Keras, you have to set `stateful=True` when define an LSTM layer. Because by default, Keras maintains the state between data within 1 batch. <b>Between batches, the state will be cleared, by default</b>. Now, if you have `stateful=True`, after the state got cleaned, you can call `reset_states()` to get your states back
    * This also means, you have to manually manage the training process 1 epoch at a time
    * You can reset state at the end of each training epoch, ready for the next training iteration
  * Also, by defualt, data samples will be shuffled within each epoch before being exposed to the network, which will break the state we need for LSTM. So, you need to set `shuffle=False`

* LSTM beginner
  * First of all, I did lots of works to make data stationary here
  * download dataset here: https://datamarket.com/data/set/22u3/international-airline-passengers-monthly-totals-in-thousands-jan-49-dec-60#!ds=22u3&display=line 
  * <b>My code - Experiment 1 (With Stationary)</b>: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/try_LSTM_Experiment1.ipynb
    * My godness, each time when there is dimensions problems in neural network, often cost me so much time to figure them out.
    * When using LSTM, it expects input format like this [sample, time_step, dimension], but when you are using Keras LSTM, it doesn't care about the amount of sample, so you are seeing I'm using `model.add(LSTM(4, input_shape=train_X.shape[1:]))`, that is to only use `(time_step, dimension)` to define input_shape
    * Also, pay attention to `Dense(1)` here, because the output should be a single output so that it can compare to `train_Y`, `test_Y`.
    * For more detailed description, check this GitHub answer, `wxs commented on Feb 5, 2016`
    * In the model, I am also using the default `sigmoid` function, This do makes sense in my case. Because my model data input comes from residual, which ranges between [-1,1]. With sigmoid function, it gets [-infinite, +infinite] X value and generates smooth range of values between 0 and 1. Now I think maybe tanh is better because it outputs the results between [-1,1]
    * Check more details for activation fucntions [AI section - Different activation functions][15]
    * At the end of this code, you will see the prediction result using ARIMA forcasting and after using LSTM. Althouh it took me so much effort to make the data stationary and to deal with the data format (when there are Keras neural network, python dataframe and numpy array, things became more complex), the final prediction visualization is difficult for normal people to understand. Customers want to see those predictions make sense, so it's better to have seasonality and trend added back in the forcasting/prediction visualization. In my case, the best stationary data cannot be converted back. Now, let me try Experiment 2, LSTM prediction with seasonality, trend added back
  * <b>My code - Experiment 2 (Without Stationary)</b>: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/try_LSTM_Experiment2.ipynb
    * As you can see, without making the data stationary, the final prediction plot makes more sense. Although compared with Experiment1, RMSE in testing data is much larger then the RMSE in training data
    * Method 1 - time t to predict time t+1. You look back 1 time interval, and the time step for LSTM is also 1
    * Method 2 - <b>WINDOW method</b>
      * It allows you to <b>use multiple recent time steps</b> to predict next step. For example, you can use time t-2, t-1, t to predict time t+1, which means you look back 3 time interval but the time step for LSTM is 1
    * Method 3 - Exchange time_step and domension
      * Compared with method 1,2, you just exchange the position of time_step and dimension in `np.reshape`
      * By doing this, you are using previous time_steps to predict t+1 time, instead of phrasing the past observations as separate input features
    * Method 4 - LSTM with memory between batches
      * The LSTM network has memory, which is <b>capable of remembering across long sequences</b>
      * Normally, after each training batch during `model.fit()`, and after`model.predict()` or `model.evaluate()`, the state in network will be reset. In this method 4, you can build state over the entire training sequence and even maintain that state if needed to make predictions.
    * Method 5 - Stacked LSTMs with Memory Between Batches
      * You just stack multiple LSTM models together
      * NOTE: This method requires an LSTM layer prior to each subsequent LSTM layer must return the sequence, so in this previous LSTM model, you set `return_sequences` as True
    * Before method 5, you can see method 1 gave the best results. But when I used method 1 settings in method 5, it gave much worse results than using method 3 settings in method 5. So, in the future, better to try all the methods and see which works better
  * reference: https://machinelearningmastery.com/time-series-prediction-lstm-recurrent-neural-networks-python-keras/
  

* LSTM Univariate Time Series Prediction
  * Univariate vs Multi-variate
    * Univariate predict next 1 step
    * Multi-variate predicts next n steps, which allows larger batch size and faster training
  * My code: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/try_LSTM_univariate_time_series.ipynb
    * Base Line Model - Walk Forward Prediction (not LSTM)
      * It is called baseline model does have its reason. You just shift testing data 1 step up and form prediction results, then compare this prediction results with testing data
    * LSTM
      * Step 1 - Data Preprocessing
        * make time series stationary (better to use LSTM without stationary to compare too, sometimes stationary data may not work better)
        * time series transfroms to supervised problem. You can just shift training data down k lags
          * In this case, training & testing vs x & y can be confusing. So, but generating supervised data, we got X, y to fit the model, X are features (here only 1 dimension), y is what to predict. As for training and testing data here are used to evaluate model performance. Both of them have X, y, we just split the data set into first 2 years (training data), lasy year (testing data)
        * make time serles scale
          * To scale data between [-1,1] is because default activation function for LSTM is tanh, which outputs the resuts between [-1,1], so it's better to have input data in this range too
      * Step 2 - Model Fitting & Evaluation
        * `batch_size` has to be a factor of the size of the training and test datasets. In this case, it is 1
        * To help neural network generate reproduciable results, you can:
          * fix seeds for both numpy and tensorflow
          * repeat experiments, and take average of RMSE, to see how well the configuration would be expected to perform on unseen data on average
          * In my code, I used both.
   * reference: https://machinelearningmastery.com/time-series-forecasting-long-short-term-memory-network-python/
     * I really don't like some naming and param settings in it
     
* LSTM Multivariate Time Series Prediction
  * After being tortured by this type of practice in unvariate time series, multivariate appears to be much easier.
  * Compared with univariate, you just need to move multiple features x steps forward
  * my code - one step forward: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/multivariate_LSTM_one_step_forward.ipynb
    * In this code, you just need to predict polution, so in fact polution is the only column you need to move forward. But the code here is moving all the original columns forward, and finally drop those unwanted columns, this method has more flexibility in case may want to predict multiple labels
    * In this code, polution just moved 1 step forward
  * my code - multiple steps forward: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/multivariate_LSTM_multi_steps_forward.ipynb
    * In this code, it moves 3 hours forward. Check my comments to find the difference between one-step forward
    * It also predicts multiple lables, check my comments
  * reference: https://machinelearningmastery.com/multivariate-time-series-forecasting-lstms-keras/
  
  
* <b>Summary of Time Series to Supervided Learning</b>
  * According to above LSTM time series predictions, I'd better to summarize major methods used in converting time series to supervised learning. It's in fact very simple. Almost the same for univariate & multivariate, single label & multiple labels.
  * Univariate: your label is your single feature moves x step forward & dropped NA
  * Multivariate, single label: create new columns, each is generated from the relevant original feature & moved x step forward & dropped NA. Then, you only keep the column that you want to predict as label, drop other newly created columns
  * Multivariate, multiple labels: create new columns, each is generated from the relevant original feature & moved x step forward & dropped NA. That's it.
  
  
******************************************************************************************

Channel Attrition

* Find key channels in a sequence

* Channel Attribution Modeling with Markov Chains
  * "An attribution model is the rule, or set of rules, that determines how credit for sales and conversions is assigned to touchpoints in conversion paths." -- From Google
    * For example in this transition disgram
    ![transition diagram](https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/transion_diagram.png)
    `P(conversion) 
     = P(C1 -> C2 -> C3 -> Conversion) + P(C2 -> C3 -> Conversion) 
     = 0.5*0.5*1*0.6 + 0.5*1*0.6 = 0.45`
  * Markov Chains maps the movement and gives a probability distribution when move from 1 state to another state
    * State space - A possible sets of a process
    * Transition operator - the probability of moving from 1 state to another state
    * Current state probability distribution - probability distribution of being in any one of the states at the start of the process
    * Transition state - In the screenshot above, channels such as C1, C2, C3 are transition states
    * Removel effect - After removing a transition state, `new probability of conversion/probability of conversion`
      * For example, removing C1 from the above, the probability of conversion is 0.5*1*0.6 = 0.3, and the remove effect is 0.3/0.45
    * Transition probability - the probability of moving from one channel to another channel
  * Customer Journey - a sequence of channels, it's a chain of Markov Graph where each vertex is a state, each edge represents transition probability of moving from 1 state to another. It is memory-less Markov Chain, since the probability of reaching to current state only depends on the previous state
  * R ChannelArrition package: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/markov_vs_heuristics.pdf
    * I really like the description here. 
    * Heristics Model
      * <b>First Touch Approach</b>: Reward the channel first start the action
      * <b>Last Touch Approach</b>: Reward the last channel made the conversion
      * <b>Linear Approach</b>: Give the same credit to all the channels on the path to the conversion
      * <b>Time Decay Approach</b>: Give subjective weights to each channel on the path to the conversion
    * Markov Model
      * Only care about toal conversion values
      * In first order markov chains, the reaching channel only depends on the previous channel
      * Varied-order Markov Chains (VOM): https://en.wikipedia.org/wiki/Variable-order_Markov_model
  * My code: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/markov_chains_beginner.R
    * Get the data here: https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/Channel_attribution.csv
    * By setting `order` in `markov_model`, you can decide k-order markov chains. Here I'm using fitst order
  * Install packages such as "ChannelAttribution.tar.gz"
    * My goodness, I have never spent longer time to install an R package like this one
    * If you check online, they told you many different methods to install R package when you had failure
    * Now I think, a good starting point is to install from GitHub, if that package has a GitHub version, because the error messages in this install method is more detailed
      * `library(devtools)`
      * `install_github("cran/ChannelAttribution")`
      * Its GitHub: https://github.com/cran/ChannelAttribution
    * In this case, I got error telling me things maybe related to "clang", maybe related to "-lgfortran", I thought mayb ethat clang error caused by "-lgfortran", so I found the solution here:
      * Install -lgfortran: https://thecoatlessprofessor.com/programming/rcpp-rcpparmadillo-and-os-x-mavericks--lgfortran-and--lquadmath-error/
      * For mac Latest OS, download it here: http://gcc.gnu.org/wiki/GFortranBinaries#MacOS
      * Install the .dmg file
      * Then open you terminal, no matter which folder you are you, just type:
        * `mkdir ~/.R`, if you have already had this folder, the terminal you tell you that you had it. If you had it, type `cd ~/.R`, if `Makevars` is there, it's great.
        * Type `cat << EOF >> ~/.R/Makevars`
        * Type `FLIBS=-L/usr/local/gfortran/lib/gcc/x86_64-apple-darwin16/6.3.0 -L/usr/local/gfortran/lib -lgfortran -lquadmath -lm`, change the version name if you downloaded another version
        * Type `EOF`
      * Now in your RStudio, type `library(devtools)`, `install_github("cran/ChannelAttribution")`
    * You may still get the error after install&compile successfully. In this pack, it is important to check source code "LinkingTo"
      * Source: https://www.rdocumentation.org/packages/ChannelAttribution/versions/1.10
      * In LinkingTo, you will see `Rcpp`, `RcppArmadillo`. Install them all and restart your R session
    * Other note
      * Install from local file if you have downloaded the package
        * `install.packages("[zipped package local location]", repos = NULL, type="source")`
  * reference: https://www.analyticsvidhya.com/blog/2018/01/channel-attribution-modeling-using-markov-chains-in-r/?utm_source=feedburner&utm_medium=email&utm_campaign=Feed%3A+AnalyticsVidhya+%28Analytics+Vidhya%29 


******************************************************************************************

READING NOTES

* Outliers Detection for Temporary Data
  * [Find the Book Here][12]
  * [Reading Notes][13]


[1]:https://github.com/hanhanwu/Hanhan_Data_Science_Resources/blob/master/README.md
[2]:https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/ARMA_example.png
[3]:https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/ts_r_intro.pdf
[4]:https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/time_series_beginner.R
[5]:http://www.stat.pitt.edu/stoffer/tsa4/R_toot.htm
[6]:https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/time_series_predition.R
[7]:https://www.analyticsvidhya.com/blog/2015/12/complete-tutorial-time-series-modeling/?utm_content=buffer529c5&utm_medium=social&utm_source=facebook.com&utm_campaign=buffer
[8]:https://www.datacamp.com/courses/arima-modeling-with-r
[9]:https://github.com/hanhanwu/Hanhan_Data_Science_Practice/blob/master/sequencial_analysis/time_series_beginner_summary.R
[10]:https://en.wikipedia.org/wiki/Autoregressive_model#Definition
[11]:https://en.wikipedia.org/wiki/Autoregressive_model#Graphs_of_AR.28p.29_processes
[12]:https://www.amazon.com/Detection-Temporal-Synthesis-Knowledge-Discovery/dp/1627053751
[13]:https://github.com/hanhanwu/readings/blob/master/ReadingNotes_Temporal_Outlier_Detection.md
[14]:https://github.com/hanhanwu/Hanhan_Data_Science_Practice
[15]:https://github.com/hanhanwu/Hanhan_Data_Science_Resources2/blob/master/README.md
