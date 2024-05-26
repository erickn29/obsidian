Давайте обсудим, как в [[asyncio]] работают задачи. Задача — это обертка вокруг сопрограммы, которая планирует выполнение последней в цикле событий как можно раньше. И планирование, и выполнение происходят в неблокирующем режиме. Это означает, что после создания задачи, можно сразу приступить к выполнению другого кода, пока эта задача работает в фоне.

Для создания задачи служит функция `asyncio.create_task`. Ей передается сопрограмма для выполнения, а в ответ она немедленно возвращает объект задачи. Этот объект можно включить в выражение `await`, чтобы получить возвращенное значение по завершении задачи.

### Пример кода с `asyncio.create_task`

```python
import asyncio

async def delay(delay_seconds: int) -> int:
    print(f'засыпаю на {delay_seconds} с')
    await asyncio.sleep(delay_seconds)
    print(f'сон в течение {delay_seconds} с закончился')
    return delay_seconds

async def main():
    sleep_for_three = asyncio.create_task(delay(3))
    print(type(sleep_for_three))
    result = await sleep_for_three
    print(result)

asyncio.run(main())
```

В этом примере предложение печати выполняется сразу после запуска задачи. Если бы мы просто использовали `await` для сопрограммы `delay`, то увидели бы сообщение только через 3 секунды.

### Конкурентное выполнение нескольких задач

Создавая задачи мгновенно и планируя их выполнение как можно раньше, мы можем конкурентно выполнять несколько длительных задач:

```python
import asyncio

async def delay(delay_seconds: int) -> int:
    print(f'засыпаю на {delay_seconds} с')
    await asyncio.sleep(delay_seconds)
    print(f'сон в течение {delay_seconds} с закончился')
    return delay_seconds

async def main():
    sleep_for_three = asyncio.create_task(delay(3))
    sleep_again = asyncio.create_task(delay(3))
    sleep_once_more = asyncio.create_task(delay(3))
    
    await sleep_for_three
    await sleep_again
    await sleep_once_more

asyncio.run(main())
```

В точке, где встречается первое после создания задачи предложение `await`, все ожидающие задачи начинают выполняться, так как `await` запускает очередную итерацию цикла событий.

### Пример с `await` и задачами

```python
import asyncio

async def delay(delay_seconds: int) -> int:
    print(f'засыпаю на {delay_seconds} с')
    await asyncio.sleep(delay_seconds)
    print(f'сон в течение {delay_seconds} с закончился')
    return delay_seconds

async def hello_every_second():
    for i in range(2):
        await asyncio.sleep(1)
        print("пока я жду, исполняется другой код!")

async def main():
    first_delay = asyncio.create_task(delay(3))
    second_delay = asyncio.create_task(delay(3))
    
    await hello_every_second()
    await first_delay
    await second_delay

asyncio.run(main())
```

В этом примере мы запускаем две задачи, которые "спят" в течение 3 секунд. Пока эти задачи простаивают, мы видим, как каждую секунду печатается сообщение «пока я жду, исполняется другой код!». Это означает, что даже во время выполнения длительных операций наше приложение может выполнять другие задачи.

Используя `asyncio` и концепции, рассмотренные выше, мы можем организовать эффективное выполнение задач, которые требуют длительного времени, не блокируя при этом основное приложение.