# Ecosystem Template — Repo Blueprint

## Standard pyproject.toml

```toml
[build-system]
requires = ["setuptools>=68.0", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "package-name"
version = "0.1.0"
description = "One-line description"
readme = "README.md"
license = {text = "MIT"}
requires-python = ">=3.10"
authors = [{name = "SuperInstance"}]
keywords = ["ai", "plato", "tiles"]
classifiers = [
    "Development Status :: 3 - Alpha",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: MIT License",
    "Programming Language :: Python :: 3",
    "Programming Language :: Python :: 3.10",
    "Topic :: Scientific/Engineering :: Artificial Intelligence",
]

[project.optional-dependencies]
dev = ["pytest>=7.0", "pytest-cov"]
all = ["numpy", "torch"]

[tool.setuptools.packages.find]
include = ["package_name*"]

[tool.setuptools.package-data]
package_name = ["py.typed"]

[tool.pytest.ini_options]
testpaths = ["tests"]
```

## Standard .gitignore

```
__pycache__/
*.pyc
*.pyo
*.egg-info/
dist/
build/
.pytest_cache/
*.egg
```

## Standard README badges

```
[![PyPI version](https://img.shields.io/pypi/v/{name}.svg)](https://pypi.org/project/{name}/)
[![Python](https://img.shields.io/pypi/pyversions/{name}.svg)](https://pypi.org/project/{name}/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Tests](https://img.shields.io/badge/tests-{count}%20passing-brightgreen.svg)](./tests)
```

## Common patterns found across 17 shipped packages

1. **Thread safety**: All stateful classes use `threading.Lock` or `RLock`. Never bare dicts.
2. **Optional deps**: Ecosystem packages use `try: import ... except ImportError` with `HAS_X` flags.
3. **py.typed marker**: Every package ships an empty `package_name/py.typed` for PEP 561 compliance.
4. **dataclass __repr__**: All data classes implement custom `__repr__` for debug readability.
5. **__all__ in __init__.py**: Explicit exports, never star imports.
