# Телеграм бот ChatGPT
import logging
import openai
import asyncio
from gpytranslate import Translator

from aiogram import Bot, Dispatcher, executor, types

# log
logging.basicConfig(level=logging.INFO)

# init translator
t = Translator()

# init openai
openai.api_key = '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'

# init aiogram
bot = Bot(token = '6282263339:AAEOwNQFN1wWT4Yy_j5XvDbQYnOWqD3aKC0')
dp = Dispatcher(bot)


@dp.message_handler()
async def gpt_answer(message: types.Message):
    # await message.answer(message.text)

    model_engine = "text-davinci-003"
    max_tokens = 128  # default 1024
    prompt = await t.translate(message.text, targetlang="en")
    completion = openai.Completion.create(
        engine=model_engine,
        prompt=prompt.text,
        max_tokens=max_tokens,
        temperature=0.6,
        top_p=1,
        frequency_penalty=0,
        presence_penalty=0
    )

    #await message.answer("ChatGPT: Генерирую ответ ...")
    translated_result = await t.translate(completion.choices[0].text, targetlang="ru")
    await message.answer(translated_result.text)

# run long-polling
if __name__ == "__main__":
    executor.start_polling(dp, skip_updates=True)
