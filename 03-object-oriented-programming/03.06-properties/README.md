# Переменные экземпляра


Как правило, класс может иметь два различных типа данных для формирования свойств класса. Вы уже видели один из них, когда мы смотрели на стеки.

Такое свойство класса существует тогда и только тогда, когда оно явно создано и добавлено в объект. Как Вы уже знаете, это можно сделать во время инициализации объекта, выполняемой конструктором.

Более того, это можно сделать в любой момент жизни объекта. Кроме того, любое существующее свойство может быть удалено в любое время.

Такой подход имеет некоторые важные последствия:

*   разные объекты одного и того же класса **могут обладать разными наборами свойств**;
*   должен быть способ **безопасно проверить, принадлежит ли конкретному объекту свойство**, которое Вы хотите использовать (если Вы не хотите вызывать исключение - это всегда стоит учитывать);
*   каждый объект **несет свой собственный набор свойств** - они никоим образом не мешают друг другу.


Такие переменные (свойства) называются **переменными экземпляра**.

Слово _instance (экземпляр)_ предполагает, что они тесно связаны с объектами (которые являются экземплярами классов), а не с самими классами. Давайте внимательнее посмотрим на них.

Вот пример:

```python
class ExampleClass:
    def __init__(self, val = 1):
        self.first = val

    def set_second(self, val):
        self.second = val


example_object_1 = ExampleClass()
example_object_2 = ExampleClass(2)

example_object_2.set_second(3)

example_object_3 = ExampleClass(4)
example_object_3.third = 5

print(example_object_1.__dict__)
print(example_object_2.__dict__)
print(example_object_3.__dict__)

```

Требуется одно дополнительное объяснение, прежде чем мы углубимся в детали. Посмотрите на последние три строки кода.

Объекты Python, когда они создаются, получают **небольшой набор предопределенных свойств и методов**. Каждый объект имеет их, хотите ли Вы их или нет. Одной из них является переменная с именем __dict__ (это словарь).

Переменная содержит имена и значения всех свойств (переменных), которые несет объект в настоящее время. Давайте использовать его для безопасного представления содержимого объекта.



Давайте сейчас углубимся в код:

*   класс с именем `ExampleClass` имеет конструктор, который **безоговорочно создает переменную экземпляра** с именем `first` и устанавливает его со значением, пропущенным через первый аргумент (с точки зрения пользователя класса) или второй аргумент (с точки зрения конструктора); обратите внимание на значение параметра по умолчанию - любой метод, который Вы можете сделать с обычным параметром функции, может быть применен и к методам;
*   у класса также есть метод, **который создает другую переменную экземпляра** с именем `second`;
*   мы создали три объекта класса `ExampleClass`, но все эти экземпляры различаются:

*   `exampleObject1` только имеет свойство с именем `first`;
*   `exampleObject2` имеет два свойства: `first` и `second`;
*   `exampleObject3` был расширен свойством с именем `third` просто налету, вне кода класса - это возможно и полностью допустимо.

Вывод программы ясно показывает, что наши предположения верны:

```
{'first': 1}
{'second': 3, 'first': 2}
{'third': 5, 'first': 4}
```

Здесь следует сделать один дополнительный вывод: **изменение переменной экземпляра любого объекта не влияет на все остальные объекты**. Переменные экземпляра идеально изолированы друг от друга.


Посмотрите на измененный пример.

```python
class ExampleClass:
    def __init__(self, val = 1):
        self.__first = val

    def set_second(self, val = 2):
        self.__second = val


example_object_1 = ExampleClass()
example_object_2 = ExampleClass(2)

example_object_2.set_second(3)

example_object_3 = ExampleClass(4)
example_object_3.__third = 5


print(example_object_1.__dict__)
print(example_object_2.__dict__)
print(example_object_3.__dict__)

```

Он почти такой же, как предыдущий. Разница только в именах свойств. Мы **добавили два подчеркивания (`__`)** перед ними.

Как Вы знаете, такое дополнение делает переменную экземпляра **частной** - она становится недоступной из внешнего мира.

Реальное поведение этих имен немного сложнее, поэтому давайте запустим программу. Вот наш вывод:

```
{'_ExampleClass__first': 1}
{'_ExampleClass__first': 2, '_ExampleClass__second': 3}
{'_ExampleClass__first': 4, '__third': 5}
```

Вы видите эти странные имена, полные подчеркиваний? Откуда они взялись?

Когда Python видит, что Вы хотите добавить переменную экземпляра к объекту, и Вы собираетесь делать это внутри любого из методов объекта, он **манипулирует операцией** следующим образом:

*   ставит имя класса перед вашим именем;
*   в начале ставится дополнительный знак подчеркивания.



Вот почему `__first` становится `_ExampleClass__first`.

**Имя теперь полностью доступно за пределами класса**. Вы можете запустить такой код:

```python
print(example_object_1._ExampleClass__first)
```  

и Вы получите достоверный результат без ошибок и исключений.

Как Вы можете видеть, ограничения частных переменных в действии.

**Манипуляция не будет работать, если вы добавите приватную переменную экземпляра вне кода класса**. В этом случае он будет вести себя как любое другое обычное свойство.

  
# Переменные класса

Переменная класса - это **свойство, которое существует только в одной копии и хранится вне любого объекта**.

Примечание: переменная экземпляра не существует, если в классе нет объекта; переменная класса существует в одной копии, даже если в классе нет объектов.

Переменные класса создаются не так, как их экземпляры. Пример расскажет Вам больше:

```python
class ExampleClass:
    counter = 0
    def __init__(self, val = 1):
        self.__first = val
        ExampleClass.counter += 1


example_object_1 = ExampleClass()
example_object_2 = ExampleClass(2)
example_object_3 = ExampleClass(4)

print(example_object_1.__dict__, example_object_1.counter)
print(example_object_2.__dict__, example_object_2.counter)
print(example_object_3.__dict__, example_object_3.counter)

```

Посмотрите:

*   в первом списке определения класса есть присваивание - переменной `counter` присваивается значение `0`; инициализация переменной внутри класса, но вне любого из его методов делает переменную переменной класса;
*   доступ к такой переменной выглядит так же, как доступ к любому атрибуту экземпляра - Вы можете увидеть его в теле конструктора; как видите, конструктор увеличивает переменную на единицу; по сути, переменная считает все созданные объекты.



Запуск кода приведет к следующему выводу:

```
{'_ExampleClass__first': 1} 3
{'_ExampleClass__first': 2} 3
{'_ExampleClass__first': 4} 3
```

Из этого примера вытекают два важных вывода:

*   переменные класса **не отображаются в `__dict__`** объекта (это естественно, поскольку переменные класса не являются частями объекта), но Вы всегда можете попытаться заглянуть в переменная с тем же именем, но на уровне класса - мы покажем Вам это очень скоро;
*   переменная класса **всегда представляет одно и то же значение** во всех экземплярах класса (объектах).


Изменение имени переменной класса имеет те же эффекты, что и те, с которыми Вы уже знакомы.

Посмотрите на пример. Можете ли Вы угадать его вывод?

```python
class ExampleClass:
    __counter = 0
    def __init__(self, val = 1):
        self.__first = val
        ExampleClass.__counter += 1


example_object_1 = ExampleClass()
example_object_2 = ExampleClass(2)
example_object_3 = ExampleClass(4)

print(example_object_1.__dict__, example_object_1._ExampleClass__counter)
print(example_object_2.__dict__, example_object_2._ExampleClass__counter)
print(example_object_3.__dict__, example_object_3._ExampleClass__counter)

```

Запустите программу и проверьте правильность своих прогнозов. Все работает как положено, не так ли?


Мы уже говорили Вам, что переменные класса существуют, даже если экземпляр класса (объект) не был создан.

Теперь мы воспользуемся возможностью, чтобы показать Вам **разницу между этими двумя переменными `__dict__`**, из класса и из объекта.

Посмотрите на код. Вот доказательство.

```python
class ExampleClass:
    varia = 1
    def __init__(self, val):
        ExampleClass.varia = val


print(ExampleClass.__dict__)
example_object = ExampleClass(2)

print(ExampleClass.__dict__)
print(example_object.__dict__)

```

Давайте внимательнее посмотрим на него:

1.  мы определяем один класс с именем `ExampleClass`;
2.  класс определяет одну переменную класса с именем `varia`;
3.  конструктор класса присваивает переменной значение параметра;
4.  наименование переменной является наиболее важным аспектом примера, потому что:

   *   изменение присваивания на `self.varia = val` приведет к созданию переменной экземпляра с тем же именем, что и у класса;
   *   изменение присваивания на `varia = val` будет работать с локальной переменной метода; (мы настоятельно рекомендуем Вам протестировать оба вышеупомянутых случая - Вам будет легче запомнить разницу);

5.  первая строка внеклассного кода печатает значение атрибута `ExampleClass.varia`; 

    примечание: мы используем значение до того, как будет создан самый первый объект класса.


Запустите код и проверьте его вывод.

Как видите, класс `__dict__` содержит гораздо больше данных, чем аналог его объекта. Большинство из них сейчас бесполезны - те, которые мы хотим, чтобы Вы тщательно проверили, показывает текущее значение `varia`.

Обратите внимание, что объект `__dict__` пуст - у объекта нет переменных экземпляра.


# Проверка существования атрибута

Отношение Python к созданию экземпляров объекта поднимает одну важную проблему - в отличие от других языков программирования, **Вы не можете ожидать, что все объекты одного класса имеют одинаковые наборы свойств**.

Как в примере. Посмотрите на это внимательно.

```python
class ExampleClass:
    def __init__(self, val):
        if val % 2 != 0:
            self.a = 1
        else:
            self.b = 1


example_object = ExampleClass(1)

print(example_object.a)
print(example_object.b)

```

Объект, созданный конструктором, может иметь только один из двух возможных атрибутов: `a` или `b`.

Выполнение кода даст следующий вывод:

```
1
Traceback (most recent call last):
  File ".main.py", line 11, in 
    print(example_object.b)
AttributeError: 'ExampleClass' object has no attribute 'b'
```

Как видите, доступ к несуществующему атрибуту объекта (класса) вызывает исключение `AttributeError`.


Инструкция `try-except` дает Вам возможность избежать проблем с несуществующими свойствами.

Это просто - посмотрите код.

```python
class ExampleClass:
    def __init__(self, val):
        if val % 2 != 0:
            self.a = 1
        else:
            self.b = 1


example_object = ExampleClass(1)
print(example_object.a)

try:
    print(example_object.b)
except AttributeError:
    pass

```

Как видите, это действие не очень сложное. По сути, мы только что скрыли проблему.

К счастью, есть еще один способ справиться с этой проблемой.


Python предоставляет функцию, **которая может безопасно проверять, содержит ли какой-либо объект/класс указанное свойство**. Функция называется `hasattr` и ожидает, что ей будут переданы два аргумента:

*   класс или проверяемый объект;
*   имя свойства, о существовании которого необходимо сообщить (примечание: это должна быть строка, содержащая имя атрибута, а не только имя).


Функция возвращает `True` или `False`.

Вот как это можно использовать:

```python
class ExampleClass:
    def __init__(self, val):
        if val % 2 != 0:
            self.a = 1
        else:
            self.b = 1


example_object = ExampleClass(1)
print(example_object.a)

if [!mark!]hasattr[!/mark!](example_object, 'b'):
    print(example_object.b)

```


Не забывайте, что функция `hasattr()` также может работать с классами. Вы можете использовать его, **чтобы узнать, доступна ли переменная класса**, как здесь, в примере.

```python
class ExampleClass:
    attr = 1


print(hasattr(ExampleClass, 'attr'))
print(hasattr(ExampleClass, 'prop'))

```

Функция возвращает `True`, если указанный класс содержит заданный атрибут, и `False` в противном случае.

Можете угадать вывод кода? Запустите его, чтобы проверить свои догадки.


И еще один пример - посмотрите на код ниже и попытайтесь предсказать его вывод:

```python
class ExampleClass:
    a = 1
    def __init__(self):
        self.b = 2


example_object = ExampleClass()

print(hasattr(example_object, 'b'))
print(hasattr(example_object, 'a'))
print(hasattr(ExampleClass, 'b'))
print(hasattr(ExampleClass, 'a'))

```

У вас все получилось? Запустите код, чтобы проверить свои прогнозы.

Хорошо, мы добрались до конца этого раздела. В следующем разделе мы поговорим о методах, поскольку методы управляют объектами и делают их активными.


# Основные тезисы


1. **Переменная экземпляра** - это свойство, существование которого зависит от создания объекта. У каждого объекта может быть свой набор переменных экземпляра.

    Более того, их можно свободно добавлять и удалять из объектов в течение их жизни. Все переменные экземпляра объекта хранятся в специальном словаре с именем `__dict__`, который содержится в каждом объекте отдельно.


2. Переменная экземпляра может быть скрытой, если ее имя начинается с `__`, но не забывайте, что такое свойство по-прежнему доступно извне класса с использованием **искаженного имени**, созданного как `_ClassName__PrivatePropertyName`.


3. **Переменная класса** - это свойство, которое существует ровно в одной копии, и для доступа к нему не требуется никаких созданных объектов. Такие переменные не отображаются как содержимое `__dict__`.

    Все переменные класса хранятся в специальном словаре с именем `__dict__`, который содержится в каждом классе отдельно.


4. Функция с именем `hasattr()` может быть использована для того, чтобы определить, содержит ли объект/класс определенное свойство.
    
    Например:
    
    ```python
    class Sample:
        gamma = 0 # Переменная класса.
        def __init__(self):
            self.alpha = 1 # Переменная объекта.
            self.__delta = 3 # Частная переменная объекта.
    
    
    obj = Sample()
    obj.beta = 2  # Другая переменная объекта (существует только в объекте "obj".)
    print(obj.__dict__)
    
    ```
    
    Код выводит:
    
    ```
    {'alpha': 1, '_Sample__delta': 3, 'beta': 2}
    ```

---

**Упражнение 1**

Какие свойства класса `Python` являются переменными экземпляра, а какие - переменными класса? Какие из них скрытые?  
  
```python
class Python:
    population = 1
    victims = 0
    def __init__(self):
        self.length_ft = 3
        self.__venomous = False

```

<details><summary>Проверка</summary>

`population` и `victims` являются переменными **класса**, а `length_ft` и `__venomous` - переменными **экземпляра** (последняя также **скрытая**).

</details>

---

**Упражнение 2**

Вы собираетесь присвоить обратное значение свойству `__venomous` объекта `version_2`, игнорируя тот факт, что свойство является скрытым. Как Вы это сделаете?

```python
version_2 = Python()
```

<details><summary>Проверка</summary>

```python
version_2._Python__venomous = not version_2._Python__venomous
```

</details>

---

**Упражнение 3**

Напишите выражение, которое проверяет, содержит ли объект `version_2` свойство экземпляра с именем `constrictor` (да, constr**i**ctor!).

<details><summary>Проверка</summary>

```python
hasattr(version_2, 'constrictor')
```

</details>

