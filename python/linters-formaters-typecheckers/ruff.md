Opensource formatter and linter for python written in Rust and maintained by Astral 

https://github.com/astral-sh/ruff

### Install
```shell
brew install ruff
cargo install ruff
pip install ruff
```

### Usage 
```shell
ruff check <dir> # Lint the files according to the config, add --fix to apply safe fixes
ruff format <dir> # Reformat the files according to the config
ruff rule RUF200 # Explain a rule
```

### Config
https://docs.astral.sh/ruff/configuration/

Supports `pyproject.toml` files
```toml
[tool.ruff]
# Best effort line length, both ruff and black don't have the ability to break up long strings yet
line-length = 140 

exclude = ['build'] # Exclude files/folders

[tool.ruff.lint]
# https://docs.astral.sh/ruff/rules/
select = [
    "A","B", "E", "F", "UP",
    "W292", "W293", "I", "N",
    "ICN", "G", "T20", "RET",
    "SIM", "ARG", "PTH", "ERA",
    "PL", "W", "ANN"]
```