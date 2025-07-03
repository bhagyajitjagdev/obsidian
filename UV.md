## Install

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

## Initialize Project
```python
uv init .
```

## Install Packages
```python
uv add <package@version>
```

## Install Dependencies
```python
uv sync
```

---
# Install Without Symlink

## Current List of Python
```python
uv python list
```

## Create venv
```python
$(uv python find <python version>) -m venv .venv --copies
```

## Install dependencies
```python
uv pip install -r pyproject.toml
```

## Check for symlink
```python
ls -la .venv/bin/python
```

_You can run `uv sync` after this to bring back normal uv commands but the venv with not be symlinked again_

