## Цели


*   Улучшение навыков работы учащегося со строками;
*   Использование метода `find()` для поиска строк.

## Сценарий


Давайте сыграем в игру. Мы дадим Вам две строки: одна - слово (например, "dog"), а вторая - комбинация любых символов.

Ваша задача - написать программу, которая ответит на следующий вопрос: **символы, составляющие первую строку, скрыты внутри второй строки?**

Например:

*   если вторая строка задана как "vcxzxduybfdsobywuefgas", ответ `да`;
*   если вторая строка "vcxzxdcybfdstbywuefsas", ответ `нет` (так как нет букв "d", "o" или "g" в этом порядке).

Подсказка:

*   Вы должны использовать варианты с двумя аргументами функций `pos()` внутри Вашего кода;
*   не беспокойтесь о чувствительности к регистру.

Протестируйте свой код, используя предоставленные нами данные.

## Тестовые данные


Пример ввода:

```
donor
Nabucodonosor
```  

Пример вывода:

```
да
```  

Пример ввода:

```
donut
Nabucodonosor
```  

Пример вывода:

```
нет
``` 

