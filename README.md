# Telebot

import telebot
from telebot import types
import os

TOKEN = '8003869689:AAFNRshqvqShfv87o3XSndLawJZcKTQ5bJQ'
bot = telebot.TeleBot(TOKEN)

UPLOAD_FOLDER = 'uploads/'

if not os.path.exists(UPLOAD_FOLDER):
    os.makedirs(UPLOAD_FOLDER)


@bot.message_handler(commands=['start'])
def start(message):
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    item1 = types.KeyboardButton("Выбрать изображение")
    item2 = types.KeyboardButton("Загрузить документ")
    markup.add(item1, item2)
    bot.send_message(message.chat.id, "Привет! Что вы хотите выбрать?", reply_markup=markup)


@bot.message_handler(func=lambda message: message.text == "Выбрать изображение")
def choose_image(message):
    bot.send_message(message.chat.id, "Пожалуйста, отправьте изображение.")


@bot.message_handler(func=lambda message: message.text == "Загрузить документ")
def choose_document(message):
    bot.send_message(message.chat.id, "Пожалуйста, отправьте документ.")


@bot.message_handler(content_types=['photo'])
def handle_photo(message):
    file_id = message.photo[-1].file_id
    file_info = bot.get_file(file_id)

    file_path = file_info.file_path
    downloaded_file = bot.download_file(file_path)

    file_name = os.path.join(UPLOAD_FOLDER, file_id + '.jpg')

    with open(file_name, 'wb') as new_file:
        new_file.write(downloaded_file)

    bot.reply_to(message, "Файл сохранен!")


@bot.message_handler(content_types=['document'])
def handle_document(message):
    file_id = message.document.file_id
    file_info = bot.get_file(file_id)

    file_path = file_info.file_path
    downloaded_file = bot.download_file(file_path)

    file_name = os.path.join(UPLOAD_FOLDER, message.document.file_name)

    with open(file_name, 'wb') as new_file:
        new_file.write(downloaded_file)

    bot.reply_to(message, "Файл сохранен!")


bot.polling(none_stop=True)
