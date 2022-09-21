# Ошибки, сбои и другие бедствия


**Все, что может пойти не так, пойдет не так**.

Это закон Мерфи, и он работает везде и всегда. Выполнение Вашего кода тоже может пойти не так. Если это может произойти, это произойдет.

```python
import math

x = float(input("Введите x: "))
y = math.sqrt(x)

print("Квадратный корень из", x, "равен", y)

```

Посмотрите на пример кода. Есть по крайней мере два возможных пути, которыми он может пойти не так. Видите их?

*   поскольку пользователь может вводить совершенно произвольную строку символов, **нет гарантии, что строку можно преобразовать в значение с плавающей точкой** - это первая уязвимость кода;
*   вторая - функция **`sqrt()` завершается ошибкой, если получает отрицательный аргумент**.


Вы можете получить одно из следующих сообщений об ошибке.

Что-то вроде этого:

```
Enter x: Abracadabra

Traceback (most recent call last):

  File "sqrt.py", line 3, in <module>

    x = float(input("Enter x: "))

ValueError: could not convert string to float: 'Abracadabra'
```

Или что-то вроде этого:

```
Enter x: -1

Traceback (most recent call last):

  File "sqrt.py", line 4, in <module>

    y = math.sqrt(x)

ValueError: math domain error
```

Можно ли защитить себя от таких неожиданностей? Конечно можно. Более того, Вы должны сделать это, чтобы считаться хорошим программистом.


# Исключения


Каждый раз, когда Ваш код пытается что-то сделать неправильно/глупо/безответственно/безумно/неосуществимо, Python делает две вещи:

*   он **останавливает Вашу программу**;
*   он создает особый вид данных, называемый **исключением**.

Оба эти действия называются **вызовом исключения**. Можно сказать, что Python всегда вызывает исключение (или можно сказать, что **исключение было создано**), когда он не знает, что делать с Вашим кодом.

Что будет дальше?

*   созданное исключение ожидает, что кто-то или что-то заметит его и позаботится об этом;
*   если ничего не случится, чтобы позаботиться о созданном исключении, программа будет **принудительно завершена**, и Вы увидите **сообщение об ошибке**, отправленное на консоль Python;
*   в противном случае, если об исключении позаботятся и **обработают** должным образом, приостановленная программа может быть возобновлена, и ее выполнение может быть продолжено.


Python предоставляет эффективные инструменты, которые позволяют **наблюдать за исключениями, идентифицировать их и эффективно обрабатывать**. Это возможно из-за того, что все потенциальные исключения имеют свои однозначные имена, поэтому Вы можете классифицировать их и реагировать соответствующим образом.


![The concept of exceptions](./assets/819e89cb98d0fb38e92d3f2559664b65470e476d.png)


Вы уже знаете некоторые типы исключений.

Взгляните на следующие диагностические исключения:

```
[!mark!]ValueError[!/mark!]: math domain error
```

Слово выделенное красным цветом - **имя исключения**. Давайте познакомимся с другими исключениями.

```python
value = 1
value /= 0

```

Взгляните на код. Запустите программу (заведомо ошибочную).

Вы увидите следующее сообщение:

```
Traceback (most recent call last):
File "div.py", line 2, in 
value /= 0
[!mark!]ZeroDivisionError[!/mark!]: division by zero
```


Это сообщение об ошибке называется `ZeroDivisionError`.

```python
list = []
x = list[0]

```

Посмотрите код. Что произойдет, когда Вы запустите его? Проверьте.

Вы увидите следующее сообщение:

```
Traceback (most recent call last):
File "lst.py", line 2, in 
x = list[0]
[!mark!]IndexError[!/mark!]: list index out of range
```

Это `IndexError`.


Как **управлять** исключениями? Слово `try - "пытаться"` ключ к решению проблемы.

Даже больше, это - ключевое слово.

Рецепт успеха следующий:

*   во-первых, Вы должны **_попытаться_ что-то сделать**;
*   затем Вы должны **проверить, все ли прошло хорошо**.

Но не лучше ли сначала проверить все обстоятельства, а потом делать что-либо, только если это безопасно и корректно?

Как в примере.

```python
first_number = int(input("Введите первое число: "))
second_number = int(input("Введите второе число: "))

if second_number != 0:
    print(first_number / second_number)
else:
    print("Операция не может быть выполнена.")

print("Конец.")

```

Конечно, этот способ может показаться наиболее естественным и понятным, но в действительности этот метод не облегчает программирования. Все эти проверки могут сделать **Ваш код раздутым и нечитаемым**.

Python предпочитает совершенно другой подход.

```python
first_number = int(input("Введите первое число: "))
second_number = int(input("Введите второе число: "))

try:
    print(first_number / second_number)
except:
    print("Операция не может быть выполнена.")

print("Конец.")

```

Посмотрите код. Это любимый подход Python.

Примечание:

*   ключевое слово `try` **начинает блок кода**, который работает корректно или некорректно;
*   затем Python пытается выполнить рискованное действие; в случае неудачи возникает исключение, и Python начинает искать решение;
*   ключевое слово `except` запускает фрагмент кода, который будет **выполнен, если что-то внутри блока `try` пойдет не так** - если возникнет исключение в предыдущем блоке `try`, **он здесь не будет выполнен**, поэтому код, расположенный после ключевого слова except, должен обеспечивать **адекватную реакцию** на возникшее исключение;
*   возврат на предыдущий уровень вложенности завершает конструкцию **try-except**.


Запустите код и проверьте его поведение.


Давайте подведем итоги:

```python
try:
    :
    :
except:
    :
    :

```

*   на первом шаге Python пытается выполнить все инструкции, помещенные между инструкциями `try:` и `except:`;
*   если с выполнением все в порядке и все инструкции выполняются успешно, выполнение переходит к точке после последней строки блока `except:`, и выполнение блока считается завершенным;
*   если что-то идет не так внутри блоков `try:` и `except:`, выполнение немедленно возвращается из блока в первую инструкцию, расположенную после ключевого слова `except` - это означает, что некоторые инструкции из блока могут быть молча пропущены.

```python
try:
    print("1")
    x = 1 / 0
    print("2")
except:
    print("Что-то пошло не так...")

print("3")

```

Посмотрите на код. Это поможет Вам понять этот механизм.

От дает следующий вывод:

```
1
Что-то пошло не так...
3
```

Примечание: инструкция `print("2")` потерялась в процессе исполнения.


У этого подхода есть один важный недостаток - если существует вероятность того, что более чем одно исключение может быть передано в ветку `except:`, у Вас может возникнуть **проблема с выяснением того, что на самом деле произошло**.

Как в нашем коде. Запустите его и посмотрите, что произойдет.

```python
try:
    x = int(input("Введите число: "))
    y = 1 / x
except:
    print("Что-то пошло не так...")

print("Конец.")

```

Сообщение: `Что-то пошло не так...` в консоли ничего не говорит о причине, хотя есть две возможные причины исключения:

*   не числовые данные, введенные пользователем;
*   целочисленное значение, равное `0`, присвоенное переменной `x`.


Технически существует два способа решения проблемы:

*   Создать два последовательных блока `try-except`, один для каждой возможной причины исключения (легко, но приведет к неблагоприятному росту кода);
*   использовать более продвинутый вариант инструкции.

Взгляните на следующую конструкцию:

```python
try:
    :
except exc1:
    :
except exc2:
    :
except:
    :

```  

Как это работает:

*   если ветвь `try` вызывает исключение `exc1`, оно будет обрабатываться блоком `except exc1:`;
*   аналогично, если ветвь `try` вызывает исключение `exc2`, оно будет обрабатываться блоком `except exc2:`;
*   если ветвь `try` вызывает любое другое исключение, оно будет обрабатываться безымянным блоком `except`.

Давайте перейдем к следующей части курса и посмотрим на механизм в действии.

Взгляните на код, наше решение будет следующим.

```python
try:
    x = int(input("Введите число: "))
    y = 1 / x
    print(y)
except ZeroDivisionError:
    print("На ноль делить нельзя.")
except ValueError:
    print("Вы должны ввести числовое значение.")
except:
    print("Что-то пошло не так...")

print("Конец.")

```

Код при запуске выдает один из следующих четырех вариантов вывода:

*   если Вы ввели действительное, не нулевое целое значение, например `5` он скажет:  
          
    ```
    0.2
    Конец.
    ```
        
*   если ввели `0`, он скажет:  
          
    ```
    На ноль делить нельзя.
    Конец.
    ```
        
*   если Вы введете любую не целочисленную строку, Вы увидите:  
          
    ```
    Вы должны ввести числовое значение.
    Конец.
    ```
        
*   если Вы нажмете Ctrl-C, пока программа ожидает ввода пользователя (что вызывает исключение с именем `KeyboardInterrupt` (прерывание клавиатуры)), программа скажет:  
    
    ```
    Что-то пошло не так...
    Конец.
    ```


Не забывайте следующее:

*   ветви `except` ищутся в том же порядке, в котором они появляются в коде;
*   Вы не должны использовать более одной ветки `except` с определенным именем исключения;
*   количество различных ветвей `except` произвольно - единственное условие - если Вы используете `try`, Вы должны указать хотя бы один `except` (определенный или нет) после него;
*   ключевое слово `except` нельзя использовать без предшествующего `try`;
*   если выполняется какая-либо из ветвей `except`, никакие следующие ветки не будут посещаться;
*   если ни одна из указанных ветвей `except` не соответствует созданному исключению, исключение остается необработанным (мы обсудим это в ближайшее время);
*   если существует неназванная ветвь `except` (одна без имени исключения), она должна быть указана как последняя.

```python
try:
    :
except exc1:
    :
except exc2:
    :
except:
    :

```

Давайте продолжим эксперименты.


Посмотрите на код. Предыдущая программа была изменена - мы удалили ветку `ZeroDivisionError`.

```python
try:
    x = int(input("Введите число: "))
    y = 1 / x
    print(y)
except ValueError:
    print("Вы должны ввести числовое значение.")
except:
    print("Что-то пошло не так...")

print("Конец.")

```

Что происходит сейчас, если пользователь вводит `0`?

Поскольку **нет отдельных ветвей** для деления на ноль, созданное исключение попадает в **общую (неназванную) ветвь**; это означает, что в этом случае программа скажет:

```
Что-то пошло не так...
Конец.
```


Попробуйте сами. Запустите программу.


Давайте еще раз испортим код.

Посмотрите на программу. На этот раз мы удалили безымянную ветвь.

```python
try:
    x = int(input("Введите число: "))
    y = 1 / x
    print(y)
except ValueError:
    print("Вы должны ввести числовое значение.")

print("Конец.")

```

Пользователь вводит `0` еще раз и:

*   возникшее исключение не будет обработано `ValueError` - оно не имеет к этому никакого отношения;
*   поскольку другой ветки нет, Вы должны увидеть это сообщение:  
          
    ```
    Traceback (most recent call last):
    File "exc.py", line 3, in 
    y = 1 / x
    ZeroDivisionError: division by zero
    ```

Вы многое узнали об обработке исключений в Python. В следующем разделе мы сосредоточимся на встроенных исключениях Python и их иерархиях.

  
  
# Основные тезисы


1. Исключение - это событие во время выполнения программы, вызванное нештатной ситуацией. Исключение следует обработать, чтобы избежать остановки программы. Часть Вашего кода, которая предположительно является источником исключения, должна быть помещена в ветку `try`.

    Когда возникает исключение, выполнение кода не прекращается, а вместо этого переходит в ветвь `except`. Это место, где должна происходить обработка исключения. Общая схема такой конструкции выглядит следующим образом:
    
    ```python
    :
    # Код, который всегда работает без сбоев.
    :
    try:
        :
        # Рискованный код.
        :
    except:
        :
        # Кризисное управление происходит здесь.
        : 
    :
    # Возвращение к нормальному выполнению.
    :
    
    ```

2. Если Вам нужно обработать несколько исключений, исходящих из одной и той же ветки `try`, Вы можете добавить более одной ветки `except`, но Вы должны пометить их разными именами исключений, например:
    
    ```python
    :
    # Код, который всегда работает без сбоев.
    :
    try:
        :
        # Рискованный код.
        :
    except Except_1:
        # Кризисное управление происходит здесь.
    except Except_2:
        # Спасаем мир здесь.
    :
    # Возвращение к нормальному выполнению.
    :
    
    ```
    
    В лучшем случае выполняется одна из ветвей `except` - ни одна из ветвей не выполняется, если возникшее исключение не соответствует ни одному из указанных исключений.


3. Вы не можете добавить более одной анонимной (безымянной) ветки `except` после названных.

```python
:
# Код, который всегда работает без сбоев.
:
try:
    :
    # Рискованный код.
    :
except Except_1:
    # Кризисное управление происходит здесь.
except Except_2:
    # Спасаем мир здесь.
except:
    # Все остальные исключения обрабатываются здесь.
:
# Возвращение к нормальному выполнению.
:
```

---

**Упражнение 1**

Каков ожидаемый вывод следующего кода?

```python
try:
    print("Let's try to do this")
    print("#"[2])
    print("We succeeded!")
except:
    print("We failed")
print("We're done")

```

<details><summary>Проверка</summary>

```
Let's try to do this
We failed
We're done
```

</details>

---

**Упражнение 2**

Каков ожидаемый вывод следующего кода?

```python
try:
    print("alpha"[1/0])
except ZeroDivisionError:
    print("zero")
except IndexingError:
    print("index")
except:
    print("some")

```

<details><summary>Проверка</summary>

```
zero
```

</details>

