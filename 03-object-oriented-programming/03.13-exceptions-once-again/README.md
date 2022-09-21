# Подробнее об исключениях

Обсуждение объектного программирования предлагает очень хорошую возможность вернуться к исключениям. Объективная природа исключений Python делает их очень гибким инструментом, способным соответствовать конкретным потребностям, даже тем, о которых Вы еще не знаете.

Прежде чем мы углубимся в **объектную грань исключений**, мы хотим показать Вам некоторые синтаксические и семантические аспекты того, как Python обрабатывает блок try-exept, поскольку он предлагает немного больше, чем что мы показали до этого.

Первая особенность, которую мы хотим обсудить здесь, - это дополнительная возможная ветвь, которая может быть размещена внутри (или, скорее, непосредственно позади) блока `try-exept` - это часть кода, начинающаяся с `else` - так же, как в примере.

```python
def reciprocal(n):
    try:
        n = 1 / n
    except ZeroDivisionError:
        print("Division failed")
        return None
    else:
        print("Everything went fine")
        return n


print(reciprocal(2))
print(reciprocal(0))

```

Код, помеченный таким образом, выполняется, тогда (и только тогда) когда не возникло никаких исключений в части `try:`. Можно сказать, что после `try` может быть выполнена ровно одна ветвь - либо та, которая начинается с `exept` (не забывайте, что может быть несколько ветвей такого рода) или та, которая начинается с `else`.

Примечание. Ветвь `else:` должна располагаться после последней ветки `exept`.

Код дает следующий вывод:

```
Everything went fine
0.5
Division failed
None
```

Блок `try-except` можно расширить еще одним способом - добавив часть, начинающуюся с ключевого слова `finally` (это должна быть последняя ветвь кода, предназначенная для обработки исключений).

Примечание: эти два варианта (`else` и `finally`) никоим образом не зависят друг от друга, и они могут сосуществовать или вызываться независимо.

Блок `finally` всегда выполняется (он завершает выполнение блока `try-except`, отсюда его имя), независимо от того, что произошло раньше, даже при возникновении исключения, независимо от того, было оно обработано или нет.

Посмотрите на код.

```python
def reciprocal(n):
    try:
        n = 1 / n
    except ZeroDivisionError:
        print("Division failed")
        n = None
    else:
        print("Everything went fine")
    finally:
        print("It's time to say goodbye")
        return n


print(reciprocal(2))
print(reciprocal(0))

```

Он выводит:

```
Everything went fine
It's time to say goodbye
0.5
Division failed
It's time to say goodbye
None
```


# Исключение - это классы

Все предыдущие примеры были удовлетворены обнаружением определенного вида исключения и соответствующим образом реагировали на него. Теперь мы собираемся углубиться и заглянуть внутрь самого исключения.

Вероятно, Вы не удивитесь, узнав, что **исключения являются классами**. Кроме того, когда возникает исключение, объект класса создается и проходит все уровни выполнения программы, ища ветвь исключения, которая готова к нему.

Такой объект несет некоторую полезную информацию, которая может помочь вам точно определить все аспекты ожидающей ситуации. Для достижения этой цели Python предлагает специальный вариант предложения исключений.

```python
try:
    i = int("Hello!")
except Exception as e:
    print(e)
    print(e.__str__())

```

Как видите, оператор `except` расширен и содержит дополнительную фразу, начинающуюся с ключевого слова `as`, за которым следует идентификатор. Идентификатор предназначен для перехвата объекта исключения, чтобы вы могли проанализировать его природу и сделать правильные выводы.

Примечание: область видимости идентификатора охватывает его ветвь `except` и не идет дальше.

В примере представлен очень простой способ использования полученного объекта - просто его вывод (как Вы можете видеть, вывод производится методом `__str__()`) и содержит краткое сообщение с описанием причины.

То же сообщение будет напечатано, если в коде нет подходящего блока `except`, и Python вынужден обрабатывать только его.

Все встроенные исключения Python образуют иерархию классов. Нет никаких препятствий для ее расширения, если Вы считаете это разумным.

Посмотрите на код.

```python
def printExcTree(thisclass, nest = 0):
    if nest > 1:
        print("   |" * (nest - 1), end="")
    if nest > 0:
        print("   +---", end="")

    print(thisclass.__name__)

    for subclass in thisclass.__subclasses__():
        printExcTree(subclass, nest + 1)


printExcTree(BaseException)

```

Эта программа выводит все предопределенные классы исключений в виде древовидной распечатки.

Поскольку **дерево является прекрасным примером рекурсивной структуры данных**, рекурсия, кажется, - это лучший инструмент для прохождения по нему. Функция `printExcTree()` принимает два аргумента:

*   точку внутри дерева, с которой мы начинаем его обход;
*   уровень вложенности (мы будем использовать его для создания упрощенного чертежа ветвей дерева).

Начнем с корня дерева - корнем классов исключений в Python является класс `BaseException` (это суперкласс всех других исключений).

Для каждого из обнаруженных классов выполните один и тот же набор операций:

*   вывести его имя, взятое из свойства `__name__`;
*   перебрать список подклассов, предоставляемых методом `__subclasses__()`, и рекурсивный вызов функцию `printExcTree()`, соответственно увеличивая уровень вложенности.


Обратите внимание, как мы нарисовали ветви и вилки. Вывод никак не сортируется - Вы можете попытаться отсортировать его самостоятельно. Более того, есть некоторые неточности в том, как некоторые ветви представлены. Это тоже можно исправить, если хотите.

Вот как это выглядит:

```
BaseException
   +---Exception
   |   +---TypeError
   |   +---StopAsyncIteration
   |   +---StopIteration
   |   +---ImportError
   |   |   +---ModuleNotFoundError
   |   |   +---ZipImportError
   |   +---OSError
   |   |   +---ConnectionError
   |   |   |   +---BrokenPipeError
   |   |   |   +---ConnectionAbortedError
   |   |   |   +---ConnectionRefusedError
   |   |   |   +---ConnectionResetError
   |   |   +---BlockingIOError
   |   |   +---ChildProcessError
   |   |   +---FileExistsError
   |   |   +---FileNotFoundError
   |   |   +---IsADirectoryError
   |   |   +---NotADirectoryError
   |   |   +---InterruptedError
   |   |   +---PermissionError
   |   |   +---ProcessLookupError
   |   |   +---TimeoutError
   |   |   +---UnsupportedOperation
   |   |   +---herror
   |   |   +---gaierror
   |   |   +---timeout
   |   |   +---Error
   |   |   |   +---SameFileError
   |   |   +---SpecialFileError
   |   |   +---ExecError
   |   |   +---ReadError
   |   +---EOFError
   |   +---RuntimeError
   |   |   +---RecursionError
   |   |   +---NotImplementedError
   |   |   +---_DeadlockError
   |   |   +---BrokenBarrierError
   |   +---NameError
   |   |   +---UnboundLocalError
   |   +---AttributeError
   |   +---SyntaxError
   |   |   +---IndentationError
   |   |   |   +---TabError
   |   +---LookupError
   |   |   +---IndexError
   |   |   +---KeyError
   |   |   +---CodecRegistryError
   |   +---ValueError
   |   |   +---UnicodeError
   |   |   |   +---UnicodeEncodeError
   |   |   |   +---UnicodeDecodeError
   |   |   |   +---UnicodeTranslateError
   |   |   +---UnsupportedOperation
   |   +---AssertionError
   |   +---ArithmeticError
   |   |   +---FloatingPointError
   |   |   +---OverflowError
   |   |   +---ZeroDivisionError
   |   +---SystemError
   |   |   +---CodecRegistryError
   |   +---ReferenceError
   |   +---BufferError
   |   +---MemoryError
   |   +---Warning
   |   |   +---UserWarning
   |   |   +---DeprecationWarning
   |   |   +---PendingDeprecationWarning
   |   |   +---SyntaxWarning
   |   |   +---RuntimeWarning
   |   |   +---FutureWarning
   |   |   +---ImportWarning
   |   |   +---UnicodeWarning
   |   |   +---BytesWarning
   |   |   +---ResourceWarning
   |   +---error
   |   +---Verbose
   |   +---Error
   |   +---TokenError
   |   +---StopTokenizing
   |   +---Empty
   |   +---Full
   |   +---_OptionError
   |   +---TclError
   |   +---SubprocessError
   |   |   +---CalledProcessError
   |   |   +---TimeoutExpired
   |   +---Error
   |   |   +---NoSectionError
   |   |   +---DuplicateSectionError
   |   |   +---DuplicateOptionError
   |   |   +---NoOptionError
   |   |   +---InterpolationError
   |   |   |   +---InterpolationMissingOptionError
   |   |   |   +---InterpolationSyntaxError
   |   |   |   +---InterpolationDepthError
   |   |   +---ParsingError
   |   |   |   +---MissingSectionHeaderError
   |   +---InvalidConfigType
   |   +---InvalidConfigSet
   |   +---InvalidFgBg
   |   +---InvalidTheme
   |   +---EndOfBlock
   |   +---BdbQuit
   |   +---error
   |   +---_Stop
   |   +---PickleError
   |   |   +---PicklingError
   |   |   +---UnpicklingError
   |   +---_GiveupOnSendfile
   |   +---error
   |   +---LZMAError
   |   +---RegistryError
   |   +---ErrorDuringImport
   +---GeneratorExit
   +---SystemExit
   +---KeyboardInterrupt
```


# Подробная анатомия исключений

Давайте подробнее рассмотрим объект исключения, поскольку здесь есть несколько действительно интересных элементов (мы вернемся к этой проблеме вскоре, когда рассмотрим базовые методы ввода/вывода Python, поскольку их подсистема исключений немного расширяет эти объекты).

Класс `BaseException` представляет свойство с именем `args`. Это кортеж, **предназначенный для сбора всех аргументов, переданных конструктору класса**. Он пуст, если конструкция была вызвана без каких-либо аргументов, или содержит только один элемент, когда конструктор получает один аргумент (здесь мы не учитываем аргумент `self`) и т.д.

Мы подготовили простую функцию для элегантного вывода свойства `args`.

```python
def print_args(args):
    lng = len(args)
    if lng == 0:
        print("")
    elif lng == 1:
        print(args[0])
    else:
        print(str(args))


try:
    raise Exception
except Exception as e:
    print(e, e.__str__(), sep=' : ' ,end=' : ')
    print_args(e.args)

try:
    raise Exception("my exception")
except Exception as e:
    print(e, e.__str__(), sep=' : ', end=' : ')
    print_args(e.args)

try:
    raise Exception("my", "exception")
except Exception as e:
    print(e, e.__str__(), sep=' : ', end=' : ')
    print_args(e.args)

```

Мы использовали функцию для печати содержимого свойства `args` в трех разных случаях, когда исключение класса `Exception` вызывается тремя разными способами. Чтобы сделать его более впечатляющим, мы также напечатали сам объект вместе с результатом вызова `__str__()`.

Первый случай выглядит рутинно - после ключевого слова `raise` есть только имя `Exception`. Это означает, что объект этого класса был создан самым обычным способом.

Второй и третий случаи могут показаться немного странными на первый взгляд, но здесь нет ничего странного - это всего лишь вызовы конструктора. Во втором операторе `raise` конструктор вызывается с одним аргументом, а в третьем - с двумя.

Как вы можете видеть, выходные данные программы отражают это, показывая соответствующее содержимое свойства `args`:

```
 :  :
my exception : my exception : my exception
('my', 'exception') : ('my', 'exception') : ('my', 'exception')
```


# Как создать собственное исключение

Иерархия исключений не является ни закрытой, ни законченной, и Вы всегда можете расширить ее, если хотите или если Вам нужно создать свой собственный мир, заполненный Вашими собственными исключениями.

Это может быть полезно, когда Вы создаете сложный модуль, который обнаруживает ошибки и вызывает исключения, и Вы хотите, чтобы исключения легко отличались от любых других, представленных Python.

Это достигается путем **определения собственных, новых исключений как подклассов, производных от предопределенных**.

Примечание: если Вы хотите создать исключение, которое будет использоваться как специализированный случай любого встроенного исключения, извлеките его только из этого. Если Вы хотите создать свою собственную иерархию и не хотите, чтобы она была тесно связана с деревом исключений Python, выведите его из любого из верхних классов исключений, например `Exception`.

Представьте, что Вы создали совершенно новую арифметику, управляемую Вашими собственными законами и теоремами. Понятно, что деление тоже было переопределено и должно вести себя не так, как обычное деление. Также ясно, что это новое деление должно вызывать свое собственное исключение, отличное от встроенного `ZeroDivisionError`, но разумно предположить, что в некоторых обстоятельствах Вам (или пользователям Вашей арифметики) может потребоваться одинаковое отношение ко всем делениям на ноль.

Подобные требования могут быть выполнены способом, представленным в редакторе. Посмотрите на код, и давайте проанализируем его:

*   Мы определили наше собственное исключение с именем `MyZeroDivisionError`, полученное из встроенного `ZeroDivisionError`. Как видите, мы решили не добавлять новые компоненты в класс;  
      
    В сущности, исключение этого класса можно, в зависимости от желаемой точки зрения, рассматривать его как обычный `ZeroDivisionError` или рассматривать отдельно;
  
*   Функция `doTheDivision()` вызывает исключение `MyZeroDivisionError` или `ZeroDivisionError`, в зависимости от значения аргумента;  
      
    Функция вызывается в общей сложности четыре раза, в то время как первые два вызова обрабатываются с использованием только одной ветви `except` (более общей) и двух последних с двумя разными ветвями, с возможностью различать исключения (не забывайте: порядок ветвей имеет принципиальное значение!).


Когда Вы собираетесь построить совершенно новую вселенную, наполненную совершенно новыми существами, которые не имеют ничего общего со всеми знакомыми вещами, Вы можете **создать свою собственную структуру исключений**.

Например, если Вы работаете с большой системой моделирования, которая предназначена для моделирования деятельности пиццерии, может оказаться желательным сформировать отдельную иерархию исключений.

Вы можете начать его создание, **определив общее исключение как новый базовый класс** для любого другого специализированного исключения. Мы сделали это следующим образом:

```python
class PizzaError(Exception):
    def __init__(self, pizza, message):
        Exception.__init__(self, message)
        self.pizza = pizza

```

Примечание: здесь мы собираем более конкретную информацию, чем обычная исключительная ситуация, поэтому наш конструктор будет принимать два аргумента:

*   указание пиццы в качестве субъекта процесса;
*   содержащий более или менее точное описание проблемы.

Как видите, мы передаем второй параметр конструктору суперкласса и сохраняем первый в нашем собственном свойстве.

Более конкретная проблема (например, избыток сыра) может потребовать более конкретного исключения. Можно получить новый класс из уже определенного класса `PizzaError`, как мы сделали здесь:

```python
class TooMuchCheeseError(PizzaError):
    def __init__(self, pizza, cheese, message):
        PizzaError._init__(self, pizza, message)
        self.cheese = cheese

```

Исключению `TooMuchCheeseError` требуется больше информации, чем обычному исключению `PizzaError`, поэтому мы добавляем его в конструктор - имя `cheese` затем сохраняется для дальнейшей обработки.

Посмотрите на код. Мы объединили два ранее определенных исключения и использовали их для работы с небольшим примером фрагмента.

```python
class PizzaError(Exception):
    def __init__(self, pizza, message):
        Exception.__init__(self, message)
        self.pizza = pizza


class TooMuchCheeseError(PizzaError):
    def __init__(self, pizza, cheese, message):
        PizzaError.__init__(self, pizza, message)
        self.cheese = cheese


def make_pizza(pizza, cheese):
    if pizza not in ['margherita', 'capricciosa', 'calzone']:
        raise PizzaError(pizza, "no such pizza on the menu")
    if cheese > 100:
        raise TooMuchCheeseError(pizza, cheese, "too much cheese")
    print("Pizza ready!")

for (pz, ch) in [('calzone', 0), ('margherita', 110), ('mafia', 20)]:
    try:
        make_pizza(pz, ch)
    except TooMuchCheeseError as tmce:
        print(tmce, ':', tmce.cheese)
    except PizzaError as pe:
        print(pe, ':', pe.pizza)

```

Один из них вызывается внутри функции `makePizza()`, когда обнаруживается любая из двух ошибочных ситуаций: неправильный запрос пиццы или запрос слишком большого количества сыра.

Примечание:

*   удаление ветви, начинающейся с исключения `TooMuchCheeseError`, приведет к тому, что все появляющиеся исключения будут классифицированы как `PizzaError`;
*   удаление ветви, начинающейся с исключения `PizzaError`, приводит к тому, что исключения `TooMuchCheeseError` остаются необработанными, и приводит к завершению программы.


Предыдущее решение, хотя и элегантное и эффективное, имеет один важный недостаток. Из-за несколько легкого способа объявления конструкторов, новые исключения не могут использоваться как есть, без полного списка необходимых аргументов.

```python
class PizzaError(Exception):
    def __init__(self, pizza='unknown', message=''):
        Exception.__init__(self, message)
        self.pizza = pizza


class TooMuchCheeseError(PizzaError):
    def __init__(self, pizza='unknown', cheese='>100', message=''):
        PizzaError.__init__(self, pizza, message)
        self.cheese = cheese


def make_pizza(pizza, cheese):
    if pizza not in ['margherita', 'capricciosa', 'calzone']:
        raise PizzaError
    if cheese > 100:
        raise TooMuchCheeseError
    print("Pizza ready!")


for (pz, ch) in [('calzone', 0), ('margherita', 110), ('mafia', 20)]:
    try:
        make_pizza(pz, ch)
    except TooMuchCheeseError as tmce:
        print(tmce, ':', tmce.cheese)
    except PizzaError as pe:
        print(pe, ':', pe.pizza)

```

Теперь, если позволяют обстоятельства, можно использовать только имена классов.

  
# Основные тезисы


1. Ветвь `else:` инструкции `try` выполняется, если во время выполнения блока `try:` не было исключения.


2. Ветвь `finally:` инструкции `try` выполняется **всегда**.


3. Синтаксис `except _Exception_Name_ as _exception_object_:` позволяет Вам перехватывать объект, несущий информацию об ожидающем исключении. Свойство объекта с именем `args` (кортеж) хранит все аргументы, переданные конструктору объекта.


4. Классы исключений могут быть расширены, чтобы обогатить их новыми возможностями или адаптировать их свойства к вновь определенным исключениям.
    
    Например:
    
    ```python
    try:
        assert __name__ == "__main__"
    except:
        print("fail", end=' ')
    else:
        print("success", end=' ')
    finally:
        print("done")
    
    ```
    
    Код выводит: `success done`.

---  

**Упражнение 1**

Каков ожидаемый вывод следующего кода?

```python
import math

try:
    print(math.sqrt(9))
except ValueError:
    print("inf")
else:
    print("fine")

```

<details><summary>Проверка</summary>

```
3.0
fine
```

</details>

---

**Упражнение 2**

Каков ожидаемый вывод следующего кода?

```python
import math

try:
    print(math.sqrt(-9))
except ValueError:
    print("inf")
else:
    print("fine")
finally:
    print("the end")

```

<details><summary>Проверка</summary>

```
inf
the end
```

</details>

---

**Упражнение 3**

Каков ожидаемый вывод следующего кода?

```python
import math

class NewValueError(ValueError):
    def __init__(self, name, color, state):
        self.data = (name, color, state)

try:
    raise NewValueError("Enemy warning", "Red alert", "High readiness")
except NewValueError as nve:
    for arg in nve.args:
        print(arg, end='! ')

```

<details><summary>Проверка</summary>

```
Enemy warning! Red alert! High readiness!
```

</details>

---


# Поздравляем! Вы завершили _Основы Python 2: Модуль 3_.

Отличная работа! Вы подошли к концу модуля 3 и завершили важный этап в своем образовании по программированию на Python. Вот краткое изложение задач, которые Вы рассмотрели и с которыми ознакомились в Модуле 3:

*   основы и основные концепции объектно-ориентированного программирования;
*   различия процедурного и объектного подходов на примере стека;
*   свойства (переменные экземпляра и класса, атрибуты)
*   методы (методы класса и объекта, конструктор, параметры и свойства)
*   концепция наследования (функции, методы, иерархии классов, полиморфизм, композиция, одиночное и множественное наследование)
*   объектно-ориентированный характер исключений Python.


Теперь Вы готовы пройти тест по модулю и попытаться выполнить последнее задание: экзамен к Модулю 3, который поможет Вам проверить, чему Вы научились.


![Module completion – congratulations](./assets/29ed2aa31a1a26221e4eac3b69d973a33cbf562e.png)





