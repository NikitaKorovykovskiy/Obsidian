Создаем схему для авторизации jwt (pip install pyjwt)
```python
from fastapi.security import OAuth2PasswordBearer
oauth2 = OAuth2PasswordBearer(tokenUrl='login')

USERS_DATA = [{"username": "john_doe", "password": "securepassword123"}]

SERCET_KEY = "mysecretkey"

ALGORITM = "HS256"
```
2 - Создаем модель User  для валидации
```python
class User(BaseModel):
    username: str
    password: str
```

3 - Функция по созданию токена с данными переданными в полезную нагрузку
```python
def create_token(data: dict):
    token = jwt.encode(data, SERCET_KEY,algorithm=ALGORITM)
    return token
```

4 - Функция получения юзера по переданному токену, раскодирование и доставание полезной нагрузки
```python
def get_user_by_token(token: str = Depends(oauth2)):
    data = jwt.decode(token, SERCET_KEY,algorithms=ALGORITM)
    return data.get("username")
```

5 - Функция получение юзера из БД
```python
def get_user_from_db(username: str):
    for user in USERS_DATA:
        if user["username"] == username:
            return user
    return None
```

6 - login авторизация через user: User + поиск по данным, если есть в бд, то создаем токен
```python
@app.post("/login")
def login(user: User):
    for user_data in USERS_DATA:
        if user.username == user_data["username"] and user.password == user_data["password"]:
            token = create_token({"username": user.username})
            return {"accsess_token": token, "token_type": "bearer"}

    return {"detail": "Invalid username or password"}
```

7 - Защищенный router передаем токен, с помощью Depends=4, если получили юзера то 200, если нет 401
```python
@app.get("/protected_resource")
def protected_resource(token: str = Depends(get_user_by_token)):
    user = get_user_from_db(token)
    if user:
        return {"message": f"Hello, {user['username']}"}
    else:
        return {"message": "Hello World"}

```

Полный код
```python
from fastapi import FastAPI, Depends
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel
import jwt


app = FastAPI()

oauth2 = OAuth2PasswordBearer(tokenUrl='login')

USERS_DATA = [{"username": "john_doe", "password": "securepassword123"}]

SERCET_KEY = "mysecretkey"
ALGORITM = "HS256"

class User(BaseModel):
    username: str
    password: str

def create_token(data: dict):
    token = jwt.encode(data, SERCET_KEY,algorithm=ALGORITM)
    return token

def get_user_by_token(token: str = Depends(oauth2)):
    data = jwt.decode(token, SERCET_KEY,algorithms=ALGORITM)
    return data.get("username")

def get_user_from_db(username: str):
    for user in USERS_DATA:
        if user["username"] == username:
            return user
    return None

@app.post("/login")
def login(user: User):
    for user_data in USERS_DATA:
        if user.username == user_data["username"] and user.password == user_data["password"]:
            token = create_token({"username": user.username})
            return {"accsess_token": token, "token_type": "bearer"}

    return {"detail": "Invalid username or password"}

@app.get("/protected_resource")
def protected_resource(token: str = Depends(get_user_by_token)):
    user = get_user_from_db(token)
    if user:
        return {"message": f"Hello, {user['username']}"}
    else:
        return {"message": "Hello World"}

```