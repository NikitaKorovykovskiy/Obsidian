
```
from collections import Counter
```

```
>>> breakfast = ['spam', 'spam', 'eggs', 'spam']
>>> breakfast_counter = Counter(breakfast)
>>> breakfast_counter
Counter({'spam': 3, 'eggs': 1})
```
Считает количество элементов в списке

Функция most_common() возвращает все элементы в убывающем порядке или
лишь те элементы, количество которых больше, чем заданный аргумент count:

```
>>> breakfast_counter.most_common()
[('spam', 3), ('eggs', 1)]
>>> breakfast_counter.most_common(1)
[('spam', 3)]
```

Счетчики можно объединять. Для начала снова взглянем на содержимое
breakfast_counter:

```
>>> breakfast_counter
>>> Counter({'spam': 3, 'eggs': 1})
```

Счетчики можно объединить с помощью оператора +:
```
>>> breakfast_counter + lunch_counter
Counter({'spam': 3, 'eggs': 3, 'bacon': 1})
```
