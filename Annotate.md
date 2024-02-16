`annotate`( _* аргументы_ , _** kwargs_ ) [¶](https://djangodoc.ru/3.2/ref/models/querysets/#django.db.models.query.QuerySet.annotate "Постоянная ссылка на это определение")

Аннотирует каждый объект в `QuerySet`предоставленном списке [выражений запроса](https://djangodoc.ru/3.2/ref/models/expressions/) . Выражение может быть простым значением, ссылкой на поле в модели (или любых связанных моделях) или агрегированным выражением (средние, суммы и т. Д.), Которое было вычислено для объектов, связанных с объектами в `QuerySet`.

Каждый аргумент для `annotate()`- это аннотация, которая будет добавлена ​​к каждому `QuerySet`возвращаемому объекту .

Например, если вы управляли списком блогов, вы можете определить, сколько записей было сделано в каждом блоге:

>>> from django.db.models import Count
>>> q = Blog.objects.annotate(Count('entry'))
# The name of the first blog
>>> q[0].name
'Blogasaurus'
# The number of entries on the first blog
>>> q[0].entry__count
42

#### `Coalesce`[¶](https://djangodoc.ru/3.2/ref/models/database-functions/#coalesce "Постоянная ссылка на этот заголовок")

_класс_`Coalesce` ( _* выражения_ , _** дополнительные_ ) [¶](https://djangodoc.ru/3.2/ref/models/database-functions/#django.db.models.functions.Coalesce "Постоянная ссылка на это определение")

Принимает список по крайней мере из двух имен полей или выражений и возвращает первое ненулевое значение (обратите внимание, что пустая строка не считается нулевым значением). Все аргументы должны быть одного типа, поэтому смешивание текста и чисел приведет к ошибке базы данных.