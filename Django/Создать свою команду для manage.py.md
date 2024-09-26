
```
from argparse import ArgumentParser
from typing import Any
from django.core.management.base import BaseCommand
from scrapy_app.run_spiders import run_active_spiders, run_spider


class Command(BaseCommand):
    """Класс Django-команды для запуска пауков."""

    help = "Запуск пауков"

    def add_arguments(self, parser: ArgumentParser) -> None:
        """Добавляет аргументы к команде.

        Args:
            parser (ArgumentParser): Парсер аргументов.
        """
        parser.add_argument(
            "spider_name",
            nargs="?",
            type=str,
            help="Имя паука для запуска",
        )

    def handle(self, *args: list, **options: dict[str, Any]) -> None:
        """Обработчик команды.

        Args:
            *args: Дополнительные позиционные аргументы.
            **options: Дополнительные именованные аргументы.
        """
        spider_name = options["spider_name"]

        if spider_name:
            # Если передано название паука, запустить только этого паука
            run_spider(spider_name)
        else:
            # Если название паука не передано, запустить всех активных пауков
            run_active_spiders()

        self.stdout.write(self.style.SUCCESS("Паук запущен успешно"))
```



Чтобы создать свою команду для запуска пауков (например, `run_spiders`) в Django, ты можешь воспользоваться механизмом пользовательских команд Django. Для этого:

1. **Создай директорию для команд**: Внутри приложения (например, `web_scraper`), где ты хочешь добавить команду, создай следующие директории и файлы, если их ещё нет:
```
your_app/
├── management/
│   ├── __init__.py
│   └── commands/
│       ├── __init__.py
│       └── run_spiders.py

```
    
2. **Создай команду `run_spiders.py`**: Внутри файла `run_spiders.py` напиши код для выполнения задачи запуска пауков. Пример:
```
import subprocess
from django.core.management.base import BaseCommand

class Command(BaseCommand):
    help = 'Run Scrapy spiders'

    def add_arguments(self, parser):
        parser.add_argument('spider_name', type=str, help='The name of the spider to run')

    def handle(self, *args, **kwargs):
        spider_name = kwargs['spider_name']
        self.stdout.write(self.style.SUCCESS(f'Running spider: {spider_name}'))

        try:
            # Команда для запуска Scrapy паука
            subprocess.run(['scrapy', 'crawl', spider_name], check=True)
            self.stdout.write(self.style.SUCCESS(f'Spider {spider_name} completed successfully'))
        except subprocess.CalledProcessError as e:
            self.stderr.write(self.style.ERROR(f'Error running spider: {e}'))

```
    
3. **Запуск команды**: Теперь ты можешь запускать свою команду через `manage.py`:
    
    `python manage.py run_spiders mnogo_mebeli`
    

Этот код будет запускать Scrapy паука с именем, указанным как аргумент (например, `mnogo_mebeli`), через команду Django.