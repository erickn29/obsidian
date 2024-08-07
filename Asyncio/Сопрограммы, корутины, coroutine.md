Сопрограмму можно представить как обычную функцию Python, обладающую особой способностью: она может приостанавливать свое выполнение, встретив операцию, которая требует значительного времени.

Для создания и приостановки сопрограммы мы используем ключевые слова async и await в Python. Слово async определяет сопрограмму, а await приостанавливает ее выполнение на время выполнения длительной операции.

Вместо стандартного def мы используем async def для определения сопрограммы. Обычная функция, такая как add_one, возвращает управление сразу же, но сопрограмма, например coroutine_add_one, не выполняется немедленно; вместо этого возвращается объект сопрограммы.

Сопрограммы не выполняются непосредственно при вызове. Вместо этого они возвращают объект, который будет выполнен позже. Для выполнения сопрограммы мы должны передать ее в цикл событий.

В asyncio добавлено несколько функций для управления циклом событий. Одна из них — asyncio.run, которую мы используем для запуска наших сопрограмм.

Функция asyncio.run создает новое событие, выполняет переданную сопрограмму до конца и возвращает результат. Она также закрывает цикл событий после завершения сопрограммы.

Цель использования [[asyncio]] состоит в том, чтобы дать циклу событий возможность выполнить другие задачи во время выполнения длительной операции. Для приостановки выполнения используется ключевое слово await, которое обычно следует за вызовом сопрограммы.

Когда интерпретатор встречает await, он приостанавливает родительскую сопрограмму и выполняет вызванную сопрограмму. По завершении выполнения await, родительская сопрограмма возобновляется, и ей передается результат выполнения вызванной сопрограммы.

Давайте посмотрим на примеры кода, которые иллюстрируют использование asyncio и сопрограмм в Python.

### Основы сопрограмм

Сопрограмму можно представить как обычную функцию, но с ключевым словом `async def`. Например:

```python
import asyncio

async def coroutine_add_one(number: int) -> int:
    return number + 1

result = asyncio.run(coroutine_add_one(1))
print(result)
```

В этом примере `asyncio.run` делает несколько важных вещей:
1. Создает новое событие.
2. Выполняет код переданной сопрограммы до конца и возвращает результат.
3. Подчищает все, что могло остаться после завершения сопрограммы.
4. Останавливает и закрывает цикл событий.

### Пример с приостановкой выполнения

Давайте рассмотрим пример, где используются `async` и `await`:

```python
import asyncio

async def add_one(number: int) -> int:
    return number + 1

async def main() -> None:
    one_plus_one = await add_one(1)
    two_plus_one = await add_one(2)
    print(one_plus_one)
    print(two_plus_one)

asyncio.run(main())
```

В этом коде, когда интерпретатор встречает выражение `await`, он приостанавливает родительскую сопрограмму и выполняет сопрограмму в выражении `await`. После завершения выполнения родительская сопрограмма возобновляется, и возвращенное значение присваивается переменной.

### Пример с задержкой

Рассмотрим более сложный пример с использованием задержки:

```python
import asyncio

async def delay(delay_seconds: int) -> int:
    print(f'засыпаю на {delay_seconds} с')
    await asyncio.sleep(delay_seconds)
    print(f'сон в течение {delay_seconds} с закончился')
    return delay_seconds

async def add_one(number: int) -> int:
    return number + 1

async def hello_world_message() -> str:
    await delay(1)
    return 'Hello World!'

async def main() -> None:
    message = await hello_world_message()
    one_plus_one = await add_one(1)
    print(one_plus_one)
    print(message)

asyncio.run(main())
```

В этом примере функция `delay` использует `await asyncio.sleep` для асинхронного ожидания. Функция `main` вызывает другие сопрограммы и использует `await` для приостановки выполнения до тех пор, пока не будут получены результаты.

Эти примеры показывают, как использовать asyncio для организации конкурентных задач и управления асинхронным вводом-выводом в Python.