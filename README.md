import discord
import requests
import os
from flask import Flask
from threading import Thread

intents = discord.Intents.default()
intents.message_content = True
client = discord.Client(intents=intents)

# Variabla për të numëruar mesazhet
message_count = 0
meme_sent = False

# Krijo një aplikacion Flask për ta mbajtur botin gjallë
app = Flask(__name__)

@app.route('/')
def home():
    return "✅ Boti është online!"

def run():
    app.run(host='0.0.0.0', port=3000)

def keep_alive():
    t = Thread(target=run)
    t.start()

@client.event
async def on_ready():
    print(f'✅ Boti është online si {client.user}')

@client.event
async def on_message(message):
    global message_count, meme_sent

    if message.author == client.user:
        return

    message_count += 1

    if not meme_sent:
        await send_meme(message.channel)
        meme_sent = True
        message_count = 0  # Rifillo numërimin

    elif meme_sent and message_count >= 20:
        await send_meme(message.channel)
        message_count = 0

# Funksion për të marrë një meme
async def send_meme(channel):
    try:
        response = requests.get("https://meme-api.com/gimme")
        meme_data = response.json()
        meme_url = meme_data["url"]
        await channel.send(meme_url)
        print("✅ Meme u dërgua!")
    except Exception as e:
        print(f"❌ Gabim gjatë dërgimit të memes: {e}")

# Fillo botin
keep_alive()
client.run(os.environ['MTIyODUxMzEwNjg4ODYyMjE5Mw.Gr2ly5.KrCj5mTBM4nzapcmJU284YHCzwUPgrCiOeG74s'])

