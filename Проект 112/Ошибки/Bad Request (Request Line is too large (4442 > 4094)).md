### Bad Request (Request Line is too large (4442 > 4094))
Ошибка из за большой длины
Решение в настройки gunicorn добавить ==limit-request-line==: 
- Либо в Dockerfile  `"--limit-request-line", "8190"` :
```python
ENTRYPOINT ["gunicorn", "config.wsgi:application", "--bind", "0.0.0.0:8080", "--workers", "8", "--timeout", "600", "--reload", "--limit-request-line", "8190"]
```
- Либо в run.sh:
```python
gunicorn iss112.wsgi:application --bind 0.0.0.0:8000 --workers 8 --limit-request-line 8190
```