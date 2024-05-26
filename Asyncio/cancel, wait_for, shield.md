Давайте обсудим, как можно управлять задачами в [[asyncio]], включая их отмену. У каждого объекта задачи есть метод `cancel`, который можно вызвать для остановки задачи. Если задача отменяется, она возбуждает исключение `CancelledError` при ожидании с помощью `await`. Это исключение можно обработать в соответствии с требованиями вашей программы.

### Пример кода с `cancel` и обработкой `CancelledError`

```python
import asyncio
from asyncio import CancelledError

async def delay(delay_seconds: int) -> int:
    print(f'засыпаю на {delay_seconds} с')
    await asyncio.sleep(delay_seconds)
    print(f'сон в течение {delay_seconds} с закончился')
    return delay_seconds

async def main():
    long_task = asyncio.create_task(delay(10))
    seconds_elapsed = 0
    while not long_task.done():
        print('Задача не закончилась, следующая проверка через секунду.')
        await asyncio.sleep(1)
        seconds_elapsed += 1
        if seconds_elapsed == 5:
            long_task.cancel()
    try:
        await long_task
    except CancelledError:
        print('Наша задача была снята')

asyncio.run(main())
```

В этом примере, если задача не завершится через 5 секунд, она будет отменена. Исключение `CancelledError` может быть возбуждено только внутри предложения `await`. Если вызвать метод `cancel`, когда задача исполняет Python-код, этот код будет продолжать работать, пока не встретится следующее предложение `await`.

### Пример с `asyncio.wait_for` и обработкой `TimeoutError`

```python
import asyncio

async def delay(delay_seconds: int) -> int:
    print(f'засыпаю на {delay_seconds} с')
    await asyncio.sleep(delay_seconds)
    print(f'сон в течение {delay_seconds} с закончился')
    return delay_seconds

async def main():
    delay_task = asyncio.create_task(delay(2))
    try:
        result = await asyncio.wait_for(delay_task, timeout=1)
        print(result)
    except asyncio.exceptions.TimeoutError:
        print('Тайм-аут!')
        print(f'Задача была снята? {delay_task.cancelled()}')

asyncio.run(main())
```

Эта программа завершается примерно через 1 секунду. По истечении 1 секунды предложение `wait_for` возбуждает исключение `TimeoutError`, которое мы обрабатываем. Мы также проверяем, была ли задача `delay` отменена.

### Пример использования `asyncio.shield`

```python
import asyncio

async def delay(delay_seconds: int) -> int:
    print(f'засыпаю на {delay_seconds} с')
    await asyncio.sleep(delay_seconds)
    print(f'сон в течение {delay_seconds} с закончился')
    return delay_seconds

async def main():
    task = asyncio.create_task(delay(10))
    try:
        result = await asyncio.wait_for(asyncio.shield(task), 5)
        print(result)
    except asyncio.exceptions.TimeoutError:
        print("Задача заняла более 5 с, скоро она закончится!")
        result = await task
        print(result)

asyncio.run(main())
```

В этом примере, если задача не завершится за 5 секунд, `asyncio.wait_for` возбуждает исключение `TimeoutError`. Однако, благодаря использованию `asyncio.shield`, сама задача не будет отменена и продолжит выполнение. После обработки исключения мы ждем завершения задачи и выводим результат.

Использование `asyncio` позволяет гибко управлять выполнением задач, включая их отмену и обработку различных сценариев, что делает его мощным инструментом для асинхронного программирования в Python.