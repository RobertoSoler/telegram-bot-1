![`telegram`](telegram-icone-icon.png)
# Projeto Telegram ChatBot 1
## Descrição
O que faz
Com o que voi construido
Porque foi construido!

palavras mais utilizadas
sentimento
https://pypi.org/project/leia-br/
produto citado - contexto
detectar intenção de compra

``` js
var increment = function

console
```
[link](license)

[link](intencao)


> [!NOTE]
> To begin, you'll need an Access Token. If you have already read and followed [[Introduction to the API|Introduction-to-the-API]], you can use the one you generated then. If not: To generate an Access Token, you have to talk to [@BotFather](https://telegram.me/botfather) and follow a few simple steps (described [here](https://core.telegram.org/bots/features#botfather)). You should really read the introduction first, though.


The `telegram.ext` submodule is built on top of the pure API implementation. It provides an easy-to-use interface and takes some work off the programmer, so you [don't have to repeat yourself](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).


**Note:** Read the article on [[Exceptions, Warnings and Logging|Exceptions,-Warnings-and-Logging]] if you want to learn more.

> [!IMPORTANT]
> This handler *must* be added last.
> If you added it before the other handlers, it would be triggered before the `CommandHandlers` had a chance to look at the update.
> Once an update is handled, all further handlers are ignored.
> To circumvent this, you can pass the keyword argument `group (int)` to `add_handler` with a value other than 0.
> See [`telegram.ext.Application.add_handler`](https://docs.python-telegram-bot.org/telegram.ext.application.html#telegram.ext.Application.add_handler) and [[this wiki page|Frequently-requested-design-patterns#how-to-handle-updates-in-several-handlers]] for details.

The `Application` class is responsible for fetching updates from the `update_queue`, which is where the [`Updater`](https://docs.python-telegram-bot.org/telegram.ext.updater.html#telegram-ext-updater) class continuously fetches new updates from Telegram and adds them to this queue.
If you create an `Application` object, using [`ApplicationBuilder`](https://docs.python-telegram-bot.org/telegram.ext.applicationbuilder.html#telegram-ext-applicationbuilder), it will automatically create an `Updater` for you and link them together with an [`asyncio.Queue`](https://docs.python.org/3/library/asyncio-queue.html#asyncio.Queue). 
You can then register handlers of different types in the `Application`, which will sort the updates fetched by the `Updater` according to the handlers you registered, and deliver them to a callback function that you defined.