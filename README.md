# velora_bot.py

from telegram import Update, Bot, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Updater, CommandHandler, CallbackContext
import random

# Backend storage for simplicity (use Firebase or DB in production)
user_data = {}

# Initialize the bot
bot_token = "YOUR_TELEGRAM_BOT_TOKEN"

def start(update: Update, context: CallbackContext):
    user_id = update.effective_user.id
    user_data[user_id] = {'tokens': 0, 'referrals': 0, 'daily_spin': False}
    update.message.reply_text("Welcome to Velora! Start mining tokens by tapping or completing tasks!")

def mining(update: Update, context: CallbackContext):
    user_id = update.effective_user.id
    if user_id in user_data:
        # Simulate mining tokens
        earned_tokens = random.randint(1, 5)
        user_data[user_id]['tokens'] += earned_tokens
        update.message.reply_text(f"You mined {earned_tokens} tokens! Current Balance: {user_data[user_id]['tokens']} tokens.")
    else:
        update.message.reply_text("Please /start first to register.")

def spin(update: Update, context: CallbackContext):
    user_id = update.effective_user.id
    if not user_data[user_id]['daily_spin']:
        # Simulate a spin
        rewards = [5, 10, 15, 20, 0]  # Different rewards
        reward = random.choice(rewards)
        user_data[user_id]['tokens'] += reward
        user_data[user_id]['daily_spin'] = True
        update.message.reply_text(f"Congrats! You won {reward} tokens from the daily spin!")
    else:
        update.message.reply_text("You've already spun the wheel today. Come back tomorrow!")

def balance(update: Update, context: CallbackContext):
    user_id = update.effective_user.id
    update.message.reply_text(f"Your current token balance is: {user_data[user_id]['tokens']} tokens.")

def main():
    updater = Updater(token=bot_token, use_context=True)
    dispatcher = updater.dispatcher

    # Command handlers
    dispatcher.add_handler(CommandHandler("start", start))
    dispatcher.add_handler(CommandHandler("mining", mining))
    dispatcher.add_handler(CommandHandler("spin", spin))
    dispatcher.add_handler(CommandHandler("balance", balance))

    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
