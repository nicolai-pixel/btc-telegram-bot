import requests
import pandas as pd
import telegram

BOT_TOKEN = '8106981967:AAGCS32Ky_rs9EFyOiAmngvhmMLLuQoDnV4'
CHAT_ID = '5069181877'
bot = telegram.Bot(token=BOT_TOKEN)

def get_data():
    url = "https://api.binance.com/api/v3/klines"
    params = {"symbol": "BTCUSDT", "interval": "5m", "limit": 100}
    response = requests.get(url, params=params)
    data = response.json()
    df = pd.DataFrame(data, columns=["time", "open", "high", "low", "close", "volume", "_", "__", "___", "____", "_____", "______"])
    df["close"] = pd.to_numeric(df["close"])
    return df

def analyze_and_send():
    try:
        df = get_data()
        last_close = df["close"].iloc[-1]
        msg = f"BTC/USDT (5m): {last_close}"
        bot.send_message(chat_id=CHAT_ID, text=msg)
    except Exception as e:
        bot.send_message(chat_id=CHAT_ID, text=f"Error: {e}")

# Schedule every 5 minutes
import time
while True:
    analyze_and_send()
    time.sleep(300)
