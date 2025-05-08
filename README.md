> import logging import nltk
>
> from nltk.corpus import wordnet from telegram import Update
>
> from telegram.ext import Updater, CommandHandler, MessageHandler,
> Filters, CallbackContext
>
> \\# Убедитесь, что скачали необходимые ресурсы NLTK
> nltk.download(\'wordnet\')
>
> \\# Включаем логирование
>
> logging.basicConfig(format=\'%(asctime)s - %(name)s - %(levelname)s
> -%(message)s\',
>
> \> level=logging.INFO)
>
> logger = logging.getLogger(\\\_\\\_name\\\_\\\_)
>
> \\# Функция для получения антонимов из WordNet def
> get_antonyms_wordnet(word):
>
> \> antonyms = set()\
> \>\
> \> for syn in wordnet.synsets(word): for lemma in syn.lemmas(): \>\
> \> if lemma.antonyms():\
> \>\
> \> antonyms.update(ant.name() for ant in lemma.antonyms()) return \>
> list(antonyms)
>
> \\# Функция для получения антонимов из Datamuse API def
> get_antonyms_datamuse(word):
>
> \> response =\
> \> requests.get(f\'https://api.datamuse.com/words?rel_ant={word}\') if
> \> response.status_code == 200:\
> \>\
> \> words = response.json()\
> \>\
> \> return \\\[w\\\[\'word\'\\\] for w in words\\\] return \\\[\\\]
>
> \\# Функция для получения антонимов из Merriam-Webster API def
> get_antonyms_merriam_webster(word):
>
> api_key = \'dab635f0-1dad-4e9e-b7cc-bb55536f7020\' \\# Замените на ваш
> API ключ
>
> response =\
> requests.get(f\'https://www.dictionaryapi.com/api/v3/references/thesaurus/
> json\
> /{word}?key={api_key}\')

\> if response.status_code == 200: data = response.json() antonyms =
\\\[\\\]

> \>\
> \> if isinstance(data, list) and len(data) \\\> 0: for entry in data:
> \>\
> \> if \'meta\' in entry and \'ants\' in entry\\\[\'meta\'\\\]:\
> \> antonyms.extend(entry\\\[\'meta\'\\\]\\\[\'ants\'\\\])\
> \>\
> \> return antonyms return \\\[\\\]
>
> \\# Функция для получения антонимов с различными источниками def
> get_antonyms(word):
>
> \> antonyms = set() antonyms.update(get_antonyms_wordnet(word)) \>
> antonyms.update(get_antonyms_datamuse(word))\
> \> antonyms.update(get_antonyms_merriam_webster(word)) return \>
> list(antonyms)
>
> \\# Команда /start
>
> def start(update: Update, context: CallbackContext) -\\\> None:
> update.message.reply_text(\'Привет! Напишите слово, и я подберу его
>
> антонимы.\')
>
> \\# Команда /help
>
> def help_command(update: Update, context: CallbackContext) -\\\> None:
> help_text = (
>
> \> \"Доступные команды:\\n\"\
> \>\
> \> \"/start - Запустить бота и получить приветственное сообщение.\\n\"
> \> \"/help - Получить информацию о командах, доступных в боте.\\n\" \>
> \"/about - Узнать информацию о проекте.\\n\"\
> \>\
> \> \"Просто напишите слово, и я подберу его антонимы!\" )\
> \>\
> \> update.message.reply_text(help_text)
>
> \\# Команда /about
>
> def about(update: Update, context: CallbackContext) -\\\> None:
> about_text = (
>
> \> \"Этот бот создан для поиска антонимов слов.\\n\"
>
> \"Цель проекта - помочь пользователям находить антонимы для обогащения
> языка.\\n\"
>
> \> \"Авторы: студенты КФУ.\\n\" \"Текущая версия бота: 1.0\\n\"\
> \>\
> \> \"Планы по обновлениям: В будущем возможно добавление новых
> функций.\" ) \>\
> \> update.message.reply_text(about_text)
>
> \\# Обработчик сообщений
>
> def echo(update: Update, context: CallbackContext) -\\\> None: word =
> update.message.text
>
> \> antonyms = get_antonyms(word)\
> \>\
> \> if antonyms:\
> \>\
> \> response = f\"Антонимы для слова \'{word}\': {\',
> \'.join(antonyms)}\" else: \>\
> \> response = f\"К сожалению, я не нашёл антонимов для слова
> \'{word}\'.\" \>\
> \> update.message.reply_text(response)
>
> def main() -\\\> None:\
> \>\
> \> updater =
> Updater(\"7841854743:AAHqvoT7UhvG5_viuz-go2ZhNq8mtUUySjI\") \>\
> \> dispatcher = updater.dispatcher\
> \>\
> \> dispatcher.add_handler(CommandHandler(\"start\", start))\
> \> dispatcher.add_handler(CommandHandler(\"help\", help_command))\
> \> dispatcher.add_handler(CommandHandler(\"about\", about))\
> \> dispatcher.add_handler(MessageHandler(Filters.text &
> \~Filters.command,
>
> echo))
>
> \> \\# Запуск бота updater.start_polling() updater.idle()
>
> if \\\_\\\_name\\\_\\\_ == \'\\\_\\\_main\\\_\\\_\': main()

