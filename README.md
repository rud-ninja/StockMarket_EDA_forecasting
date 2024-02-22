# Exploring Microsoft Stock: EDA and Forecasting

## Introduction

Hi everyone. I've analyzed Microsoft stock data and built some models. I'm new to stocks, so I won't explain what the stock market is. Instead, I'll talk about what I've done in this project and what I've found.

I've split the project into two parts:
1.	Exploratory data analysis
2.	Data modeling

Let's dive in.

## Exploratory Data Analysis

This is what a typical stock price record looks like:
1.	Date: The date of the record
2.	Open: The price when the market opened
3.	High: The highest price during the day
4.	Low: The lowest price during the day
5.	Close: The price when the market closed
6.	Volume: Total number of transactions during the day

The price features are commonly referred to as OHLC (Open, High, Low, Close).

#### Preprocessing

We'll change the date column to datetime type and set it as the dataset's index. This makes it easier to slice and search using logical operations. Splitting the dates into day, month, and year helps with group operations based on months or years.

#### Correlation among features and their trends over time

The OHLC series are pretty much the same, which makes sense since stock prices don't usually change drastically within a single day. Also, there's a moderate negative correlation between prices and volume. This means that as prices go up, fewer shares are traded, possibly because more people are selling than buying.

Apart from that, I'm interested in checking out how the numerical features are distributed.

All the distributions are skewed to the right, especially for OHLC. However, the Volume distribution might be accurate. This skewness is because average stock prices keep rising over the years. So, values below 100 may never occur again, barring a major collapse.

The distribution of OHLC is influenced by time, as shown by the boxplots below.

As mentioned earlier, stock prices generally increase over time, while volume shows a slight decline.

Note: Plotting data from 1986 to 2024 would make the graphs too long, so I'm focusing on data from 2015 onwards for clearer visuals.

Close and Volume are the key features. Let's examine their long-term trends.

The overall trend of stock prices has been upward, but there have been significant fluctuations with lasting effects. While the volume trend has remained mostly stable, it has reacted to these major price changes.

Certain news and announcements, like COVID-19, the recession warning in early 2022, the partnership with OpenAI, and other AI advancements in 2023, have sharply impacted stock prices.

#### Candlestick plot

Another way to visualize the trend of stock prices over time is through a candlestick plot. Instead of individual data points, this plot uses candlesticks, which consist of a rectangular body with two wicks on the top and bottom. Each candlestick represents the OHLC information during a selected period. 

Unlike a rolling mean, which just returns the average over n days, a candlestick plot displays the highest high, the lowest low, and the first open and last close prices during the period of n days.

Candlestick figure details:
*   The body's height in a candlestick plot shows the difference between the first open and last close prices during the chosen period. If the close price is higher than the open price, it's green; if lower, it's red.
*   The wicks denote the highest and lowest price during the period.

Each candlestick from this plot contains weekly data, i.e., data within a 7-day period.


#### Seasonal decompose

Time series data Y(t) can be broken down into three main parts: a trend component T(t), a seasonal component S(t), and the residuals (or noise) R. This breakdown helps us understand patterns in the data.
*   Additive decomposition: Y(t) = T(t) + S(t) + R
*   Multiplicative decomposition: Y(t) = T(t) x S(t) x R

Visualizing the decomposition is easier than reading formulas, so take a look below.

To determine the appropriate decomposition type, we need to analyze the residuals. Looking at the histogram, both sets of residuals show a normal distribution. The differences in mean and standard deviations between the residuals come from the nature of the decompositions.

It's usually better to have normally distributed residuals or residuals as a stationary series for a good decomposition. Residuals with their own trends and seasonality indicate an improper decomposition.

For this scenario, I've chosen multiplicative decomposition because there's a gradual rise in the trend. Also, I picture stock prices as a product of a consistent pattern of transactions (e.g., new financial year, quarterly reports) and the purchasing power of people (the trend).

I chose multiplicative decomposition for the volume data. The aim is to compare the seasonality of both the close and volume series to identify any patterns.

The seasonality of closing price and volume show a moderate negative correlation. Usually, people buy low and sell high. Looking at monthly seasonality, when stock prices rise, volume tends to drop.

#### Volume profiling

Volume profiling is a trading technique that looks at the cumulative sum of all trading volumes for each price (or price range) over a specified period. It helps traders and analysts understand the distribution of trading activity and find prices with significant buying or selling interest.

#### Volatility and momentum

To assist traders and investors in stock market decisions, two key indicators are commonly used:
1.	Volatility: This indicates how much a stock's price changes over time. High volatility means prices swing a lot, signaling higher risk, while low volatility means prices are more stable.
2.	Momentum: This shows the direction and strength of a stock's price movement over time. Traders use momentum to spot trends and potential buying or selling chances.

We can delve into these concepts with some calculations and graphs to make them clearer.

### Volatility

#### Average True Rate (ATR)

Average True Range (ATR) is a volatility measure calculated by averaging the range between a stock's high and low prices over a certain number of days. Each day's range is the highest among three possibilities: 1) difference between today's high & today's low, 2) the absolute difference between today's high & yesterday's close, and 3) the absolute difference between today's low & yesterday's close.

#### Historical volatility

Another measure of stock price volatility is the spread, which is calculated as the standard deviation of daily returns. Daily returns represent the percentage change in a stock's closing price from one day to the next. Standard deviation measures how much the daily returns deviate from the average return. A higher standard deviation means higher volatility, while a lower standard deviation means lower volatility.

#### Bollinger bands

The third way to measure price volatility is through bands. These consist of upper and lower lines around a central line. The central line is a simple moving average of the actual series over a specific period. The upper and lower lines are determined by multiplying the standard deviation during the same period by an integer. Thicker bands around the central line indicate high price volatility, while thinner regions represent more stable price periods.

### Momentum

#### Relative strength index

The Relative Strength Index (RSI) is a tool used to gauge the speed and change of price movements. It helps identify overbought or oversold conditions in a market and confirms the strength of a trend.

Here's how it's calculated:
1.	Two series are created: one for up closes (gain) from the preceding day and another for down closes (loss).
2.	If the condition isn't met for either series, a 0 is inserted to maintain the same length as the original series.
3.	Then, rolling averages are calculated for both series.
4.	Next, the relative strength (RS) of the stock price is determined by dividing the rolling average series of gain by that of loss.
5.	Finally, the RSI series is obtained by putting the RS series into the formula: 100 - (100 / (1 + RS)).

The RSI helps understand overbought and oversold conditions. Its values always range between 0 and 100. Traders often use two points, such as 30 and 70, as thresholds. When the RSI rises above 70, it suggests an overbought condition, indicating that the price may soon fall. Conversely, when it drops below 30, it suggests an oversold condition, indicating that the price may soon rise.

#### Moving average convergence divergence (MACD)

The Moving Average Convergence Divergence (MACD) is a widely used momentum indicator in technical analysis. It helps identify changes in the strength, direction, momentum, and duration of a trend in an asset's price.

MACD utilizes exponential weighting of values and consists of two lines: the MACD line and the signal line. When the MACD line crosses above the signal line, it suggests that the stock price may rise. Conversely, when it crosses below the signal line, it suggests that the price may fall.

## Forecasting

Now that we've analyzed the stock prices, let's attempt to predict some data. We'll focus on univariate forecasting and explore two approaches: 
1.	A statistical approach using ARMA.
2.	A deep learning approach using LSTM.

Afterwards, we'll compare the results from both methods. We'll focus on predicting the "Close" attribute because it's the most useful feature for forecasting.

To use the ARMA model for forecasting, the time series needs to be stationary. Our analysis shows that the closing price series has an upward trend, making it non-stationary. One way to make it stationary is by differencing, which ARIMA uses. Another approach is to use daily returns, which show the percentage change from one trading day to the next. Daily returns might be stationary, but we'll confirm this using an Augmented Dickey Fuller (ADF) test.

ADF test results:
“Close” p-value: 1.0000
“Return” p-value: 0.0000

As expected, the closing price series isn't stationary, but the daily returns series is. Forecasting daily returns is important for stock trading because it helps predict potential profit and market volatility. So, we'll focus on forecasting daily returns.

I'll test the models on both short-term and long-term forecasts. For short-term forecasts, I'll use data from three consecutive months individually to understand their robustness. For long-term forecasts, I'll merge data from all three months and check the forecasts against it. In addition to daily returns, I'll assess how well the model can determine the weekly trend and volatility of the returns.

### ARMA model

I'll use 3-fold cross-validation to find the best (p, q) parameters for the ARMA model.

I'll choose the p and q parameters based on the top 5 most significant lags from the partial autocorrelation and autocorrelation functions, respectively.

After cross-validation, the parameter combination with the lowest Mean Absolute Error was (p=8, q=7).

### LSTM model

For the deep learning model, I won't use cross-validation because there are other regularization methods. When using LSTM for forecasting, I'll split the original series into shorter sequences using a sliding window approach. The sequence length will be chosen in line with the significant auto-regressive lag indicated by the partial autocorrelation function.

I determined the window size through trial and error. If the window was too short, it couldn't capture the complex patterns of the series. Conversely, if it was too large, the forecast for initial data points was inaccurate, although it performed well in the long term. Additionally, since it's a sequence-to-sequence model, to forecast initial data points in tests, we need to augment (window_size) data from the series before it.

In the deep learning architecture I used, there's a multi-layer perceptron block that transforms the sequential data before it goes into the LSTM. This setup is a simpler way to mimic the application of Restricted Boltzmann Machines, as suggested in this paper. After the LSTM, instead of using just one fully connected layer to funnel the data to a single valued output, I added an extra layer to do more computation and make the network slightly deeper, aiming to capture more patterns in the series.

### Results

Based on the results, the ARMA model forecasts the movement of daily returns better, but struggles with capturing their extent (volatility). The LSTM also forecasts the movement of daily returns fairly well, slightly lagging behind the ARMA model, but it excels in capturing volatility. Overall, in my opinion, the LSTM provides a better output. It offers a trade-off where sacrificing a bit of accuracy in daily returns forecasting leads to significantly better insights into market volatility.

## References
