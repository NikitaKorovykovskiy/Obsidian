
В FastAPI вы можете установить файлы cookie в ответе, используя метод `` `set_cookie` `` в параметре `Response`. Этот метод позволяет вам определить имя файла cookie, значение и дополнительные параметры, такие как домен, путь, срок действия и параметры безопасности.

```python
from fastapi import FastAPI, Response
from datetime import datetime
 
app = FastAPI()
 
@app.get("/")
def root(response: Response):
    now = datetime.now().strftime("%d/%m/%Y, %H:%M:%S")   # получаем текущую дату и время
    response.set_cookie(key="last_visit", value=now)
    return  {"message": "куки установлены"}
```