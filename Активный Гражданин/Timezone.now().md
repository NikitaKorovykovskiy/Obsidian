Если указано timezone.now() и при этом стоят настройки в settings UTC
Указать 
```python
now_time = timezone.now().astimezone(timezone.pytz.timezone('Asia/Krasnoyarsk'))
```