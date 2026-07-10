
uv
ud add --dev black ruff mypy


Toml File
```
[project]
name = "backend"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.14"
dependencies = [
    ...
]

[dependency-groups]
dev = [
    "black",
    "ruff",
    "mypy",
]

[tool.black]
line-length = 88
target-version = ["py314"]

[tool.ruff]
line-length = 88
target-version = "py314"

[tool.ruff.lint]
select = ["E", "F", "I"]

[tool.mypy]
python_version = "3.14"
strict = true
```



```python
from pydantic_settings import BaseSettings, SettingsConfigDict

  
  

class Settings(BaseSettings):

model_config = SettingsConfigDict(env_file=".env")

  

DATABASE_URL: str

DEBUG: bool

SECRET_KEY: str

  
  
  
  
  

settings = Settings()
```


