Установить скрапер

```
pip install Scrapy
```

Создать проект
```
scrapy startproject tutorial
```

Установить playwright
```
pip install playwright scrapy-playwright
```
```
playwright install
```

### Настройка проекта для работы с Playwright

Откройте файл `my_scraper/my_scraper/settings.py` и добавьте следующие настройки для включения Playwright в проект:
```
# settings.py

# Включаем Playwright как обработчик загрузки
DOWNLOAD_HANDLERS = {
    "http": "scrapy_playwright.handler.ScrapyPlaywrightDownloadHandler",
    "https": "scrapy_playwright.handler.ScrapyPlaywrightDownloadHandler",
}

# Настройки для Playwright
PLAYWRIGHT_LAUNCH_OPTIONS = {
    "headless": True,  # Браузер будет работать в фоновом режиме
}

# Другие базовые настройки Scrapy
ROBOTSTXT_OBEY = True
DOWNLOAD_TIMEOUT = 60
```
