1. `pydantic-settings==2.6.1`
2. 
```
from pydantic_settings import BaseSettings

class AppSettings(BaseSettings):
    RABBIT_QUEUE: str = "default_queue"
    RABBIT_HOST: str = "localhost"

    class Config:
        env_prefix = "RABBIT_"


def setup_settings() -> AppSettings:
    return AppSettings()
```
3. `settings = setup_settings()`
