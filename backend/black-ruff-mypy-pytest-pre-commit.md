
Automatic code formatter for Python


**Instalation**

```bash
pip install black
```

**Usage**
```shell
black {source_file_or_directory}
python -m black {source_file_or_directory}
```

### Default Line Length
```
88 characters
```

If you wanna change that

```
TOML

[tool.black]
line-length=100
```




### Check Mode
```bash
black --check .
```


### Configuration
in `pyproject.toml`


```TOML

[tool.black]
line-length=88
target-version=["py312"]
include = '\.pyi?$'
exclude = '''
/( 
	\.git 
	| \.venv 
	| migrations 
	| build 
	| dist 
)/
'''

```


# ruff

Ruff is a linter and code quality tool for Python
Its primary job is to find problem in your code before they becomes bugs

### What is a linter?
A linter is a program that analyzes source code without running it


**Installing**

```bash
ud add --dev ruff
```

**Usage**
```bash
ruff check .
```

**Auto fix** 

```bash
ruff check . --fix
```


### Configuration

Ruff is configured in `pyproject.toml`

```TOML
[tool.ruff]
ling-length = 88
target-version = "py312"

[tool.ruff.lint]
select = ["E", "F"]
```
# Mypy
Mypy is a static type checker for Python

### Why do we need Mypy
Python is dynamically typed

### Instalation
```bash
uv add --dev mypy
```

### Usage
```bash
mypy {source_file_or_directory}
```


### Configuration
```TOML
[tool.mypy]
python_version = "3.12"
strict = true
```
