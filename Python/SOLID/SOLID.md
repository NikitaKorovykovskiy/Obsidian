### S - Принцип единственной ответственности: каждый класс должен отвечать только за одну задачу.
Пример без SRP
```python
class Order:
    def __init__(self, items):
        self.items = items

    def calculate_total(self):
        return sum(item['price'] for item in self.items)

    def save_to_database(self):
        print("Saving order to the database.")

    def print_order(self):
        print("Printing order details...")

```
Класс `Order` выполняет три разных задачи: расчёт стоимости, сохранение в базу данных и печать. Это нарушает SRP.

Пример с SRP
```python
class Order:
    def __init__(self, items):
        self.items = items

    def calculate_total(self):
        return sum(item['price'] for item in self.items)


class OrderSaver:
    def save(self, order):
        print("Saving order to the database.")


class OrderPrinter:
    def print(self, order):
        print("Printing order details...")

```
Теперь каждый класс отвечает только за одну задачу, что упрощает изменение и тестирование отдельных частей.


### O - Принцип открытости-закрытости: классы должны быть открыты для расширения, но закрыты для модификации.

Пример без OCP
```python
class DiscountCalculator:
    def calculate(self, amount, discount_type):
        if discount_type == "percentage":
            return amount * 0.9
        elif discount_type == "fixed":
            return amount - 10

```
Каждый раз, когда добавляется новый тип скидки, нужно менять метод `calculate`, что нарушает OCP.

Пример с OCP
```python
class DiscountStrategy:
    def apply_discount(self, amount):
        return amount


class PercentageDiscount(DiscountStrategy):
    def apply_discount(self, amount):
        return amount * 0.9


class FixedDiscount(DiscountStrategy):
    def apply_discount(self, amount):
        return amount - 10


class DiscountCalculator:
    def calculate(self, amount, discount_strategy: DiscountStrategy):
        return discount_strategy.apply_discount(amount)

```
Теперь для добавления нового типа скидки нужно просто создать новый класс, унаследованный от `DiscountStrategy`, и передать его в `DiscountCalculator`.

Создаем новый класс, от которого наследуем повторяющиеся и изменненые действия, в них реализуем свою логику, и создаем класс в который передаем аргумент и объект класса со своим новым методом и вызываем
### L - Принцип подстановки Барбары Лисков: объект должен быть заменяем на экземпляры своих подклассов без изменения корректности программы.

### I - Принцип разделения интерфейса: лучше несколько специализированных интерфейсов, чем один универсальный.

### D - Принцип инверсии зависимостей: модули верхнего уровня не должны зависеть от модулей нижнего уровня; оба типа должны зависеть от абстракций.