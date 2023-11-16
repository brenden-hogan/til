Opensource type checker for python written in python.

[https://github.com/python/mypy](https://github.com/python/mypy)

### Install:
```shell
python3 -m pip install mypy
```
### Usage:
This will run mypy on all py and pyi files in the directory specified
```shell
mypy <dir>
```

Can also put cfg in a `pyproject.toml` file
```toml
[tool.mypy]
files = ['**/*.py'] # All py files in the current directory and ones below it
```

https://mypy.readthedocs.io/en/stable/config_file.html

#### Strict Mode
Should be enabled for any new code base but might be too daunting for migrating an existing one. As far as I can tell only a cmd line argument as the flags it enables can change.
```shell
mypy --strict
```


