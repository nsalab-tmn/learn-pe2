## Цели


*   улучшение навыков учащегося в создании подклассов;
*   добавление новых функций к существующему классу.

## Сценарий


Ваша задача - немного расширить возможности класса `Queue`. Нам нужен метод без параметров, который возвращает `True`, если очередь пуста, и `False` в противном случае.

Дополните код, который мы предоставили. Запустите его, чтобы проверить, дает ли он результат, аналогичный нашему.

```python
class QueueError(???):
    pass


class Queue:
    #
    # Код из предыдущей лабораторной работы.
    #


class SuperQueue(Queue):
    #
    # Напишите здесь новый код.
    #


que = SuperQueue()
que.put(1)
que.put("dog")
que.put(False)
for i in range(4):
    if not que.isempty():
        print(que.get())
    else:
        print("Queue empty")

```

Ниже Вы можете скопировать код, который использовали в предыдущей лабораторной работе:

<details><summary>Проверка</summary>

```python
class QueueError(IndexError):
    pass


class Queue:
    def __init__(self):
        self.queue = []
    def put(self,elem):
        self.queue.insert(0,elem)
    def get(self):
        if len(self.queue) > 0:
            elem = self.queue[-1]
            del self.queue[-1]
            return elem
        else:
            raise QueueError

```

</details>

## Ожидаемый вывод

```
1
dog
False
Queue empty
```

