# python-for-techincal-analysis
import yfinance as yf
import pandas as pd
import pandas_ta as ta
import talib
import matplotlib.pyplot as plt

# Fetch historical data for Tata Steel
symbol = 'TATASTEEL.NS'  # NSE symbol for Tata Steel
# Assuming today is 16-03-2024, fetch data up until this date
data = yf.download(symbol, start="2022-01-01", end="2024-03-16")

# Calculate 50-day and 200-day Simple Moving Averages (SMA) using pandas_ta
data['SMA_50'] = data['Close'].rolling(window=50).mean()
data['SMA_200'] = data['Close'].rolling(window=200).mean()

# Calculate 14-day Relative Strength Index (RSI) using TA-Lib
data['RSI'] = talib.RSI(data['Close'], timeperiod=14)

fig, axs = plt.subplots(2, 1, figsize=(14, 10))

# Plot closing price and SMAs
axs[0].plot(data.index, data['Close'], label='Close Price', color='blue')
axs[0].plot(data.index, data['SMA_50'], label='50-Day SMA', color='red')
axs[0].plot(data.index, data['SMA_200'], label='200-Day SMA', color='green')
axs[0].set_title('Tata Steel Close Price and Moving Averages')
axs[0].set_ylabel('Price')
axs[0].legend(loc='upper left')

# Plot RSI
axs[1].plot(data.index, data['RSI'], label='RSI', color='purple')
axs[1].set_title('Tata Steel Relative Strength Index')
axs[1].set_ylabel('RSI')
axs[1].axhline(70, linestyle='--', color='grey', linewidth=0.5)
axs[1].axhline(30, linestyle='--', color='grey', linewidth=0.5)
axs[1].legend(loc='upper left')

plt.show()
# Generate signals based on SMA and RSI
# Buy if SMA_50 crosses above SMA_200 and RSI is above 50 (indicating positive momentum)
# Sell if SMA_50 crosses below SMA_200 or RSI falls below 50 (indicating negative momentum or overbought conditions)
# Hold otherwise
data['Signal'] = 'Hold'
data.loc[(data['SMA_50'] > data['SMA_200']) & (data['RSI'] > 50), 'Signal'] = 'Buy'
data.loc[(data['SMA_50'] < data['SMA_200']) | (data['RSI'] < 50), 'Signal'] = 'Sell'

# Display the last 10 entries to see the signals
print(data[['Close', 'SMA_50', 'SMA_200', 'RSI', 'Signal']].tail(10))
