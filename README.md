# Yu
Just an ai that helps ppl with daily task as an assistant and friend  useful, with also a premiumfeature.  
import discord
from discord.ext import commands
import json
import os

intents = discord.Intents.default()
bot = commands.Bot(command_prefix="!", intents=intents)

# Ensure premium.json exists
if not os.path.exists("premium.json"):
    with open("premium.json", "w") as f:
        json.dump({"premium_users": []}, f)

# Check if a user is premium
def is_premium(user_id):
    with open("premium.json", "r") as f:
        data = json.load(f)
    return str(user_id) in data["premium_users"]

@bot.event
async def on_ready():
    print(f"✅ Logged in as {bot.user}")

# ✅ PUBLIC command
@bot.command()
async def hello(ctx):
    await ctx.send(f"👋 Hello {ctx.author.name}!")

# 💎 PREMIUM command
@bot.command()
async def premium_feature(ctx):
    if not is_premium(ctx.author.id):
        await ctx.send("🚫 This command is for premium users only.")
        return
    await ctx.send("🌟 Welcome to Premium Club!")

# 👑 Admin command to add premium
@bot.command()
@commands.has_permissions(administrator=True)
async def add_premium(ctx, user: discord.User):
    with open("premium.json", "r+") as f:
        data = json.load(f)
        if str(user.id) not in data["premium_users"]:
            data["premium_users"].append(str(user.id))
            f.seek(0)
            json.dump(data, f, indent=4)
            f.truncate()
            await ctx.send(f"✅ {user.mention} is now a premium user.")
        else:
            await ctx.send("⚠️ User is already premium.")

# 🔓 Admin command to remove premium
@bot.command()
@commands.has_permissions(administrator=True)
async def remove_premium(ctx, user: discord.User):
    with open("premium.json", "r+") as f:
        data = json.load(f)
        if str(user.id) in data["premium_users"]:
            data["premium_users"].remove(str(user.id))
            f.seek(0)
            json.dump(data, f, indent=4)
            f.truncate()
            await ctx.send(f"❌ {user.mention} is no longer a premium user.")
        else:
            await ctx.send("⚠️ User is not a premium member.")

bot.run(os.getenv("DISCORD_TOKEN"))
discord.py
premium.json
{
  "premium_users": []
}requirements.txt
