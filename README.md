# Pythonist <!-- omit in toc -->

Your short, to the point guide for writing modern Python code.

- [1. Developer Experience](#1-developer-experience)
  - [1.1. Package Management with uv](#11-package-management-with-uv)
  - [1.2. Linting \& Formatting with Ruff](#12-linting--formatting-with-ruff)
  - [1.3. Testing](#13-testing)
  - [1.4. Setting up VSCode for Python](#14-setting-up-vscode-for-python)
- [2. Typings](#2-typings)

## 1. Developer Experience

### 1.1. Package Management with uv

[uv](https://docs.astral.sh/uv/) is ultra fast, makes managing virtual environments easy, and comes with many great features, like workspaces, or bytecode compilation to boost startup times.

To start using `uv` in your project, simply run:

```bash
uv init # create minimal pyproject.toml
```

**Dependency Groups & Development Dependencies:**

To maintain a clean and lightweight Docker image for deployment, install development related tools and dependencies (like linters, formatters, testing frameworks) using the --dev flag:

```bash
uv add --dev <dev-dependency-name>
```

**Managing outdated dependencies:**

You can locally list and update outdated dependencies using `uv tree`:

```bash
uv tree --depth 1 --outdated
```

You should setup [Rennovate](https://docs.astral.sh/uv/guides/integration/dependency-bots/#renovate) to automatically upgrade dependencies.

### 1.2. Linting & Formatting with Ruff

[ruff](https://docs.astral.sh/ruff/) is incredibly fast and combines the functionalities of Black (formatter), isort (import sorter), and Flake8 (linter) into a single tool.

Install Ruff as a development dependency using `uv`:

```bash
uv add --dev ruff # install ruff in the dev dependencies group
```

**Configuring Ruff:**

Ruff comes with great default settings, however to tweak it to your specific needs, simply add necessary sections in your `pyproject.toml` (refer to the [Ruff settings documentation](https://docs.astral.sh/ruff/settings/) page for more info)

```toml
[tool.ruff]
exclude = ["alembic"]
line-length = 120

[tool.ruff.lint]
fixable = ["ALL"]

[tool.ruff.format]
quote-style = "double"
```

### 1.3. Testing

Testing is essential and should be part of every Python project. [Pytest](https://docs.pytest.org/en/stable/) is the recommended testing framework. You should also install the [code coverage extension](https://pytest-cov.readthedocs.io/en/latest/) along with Pytest:

```bash
uv add --dev pytest pytest-cov
```

**On naming test files:**

Pytest offers flexibility in test file organization. A clean approach is to place test files alongside your source code, named like `test_*.py` or `*_test.py.`

**Automatic coverage:**

For immediate feedback on test coverage, configure pytest-cov directly in your `pyproject.toml`:

```toml
[tool.pytest.ini_options]
filterwarnings = ["ignore::DeprecationWarning"]
addopts = "--cov=. --cov-report html"

[tool.coverage.run]
omit = ["*_test.py"]
```

This setup will automatically generate an HTML coverage report in your project's htmlcov directory whenever you run pytest.


### 1.4. Setting up VSCode for Python

Here are the minimal recommended extensions to have a great development experience:

- [Pylance](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance)
- [Ruff](https://marketplace.visualstudio.com/items?itemName=charliermarsh.ruff)
- [MyPY](https://marketplace.visualstudio.com/items?itemName=ms-python.mypy-type-checker)

To make the most of these tools, configure your `.vscode/settings.json` file:

```.vscode/settings.json
"[python]": {
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit",
      "source.organizeImports": "explicit"
    }
  }
```

It's highly recommended to commit the `.vscode/settings.json` file to your project repository to ensure that everyone on the team uses the same consistent development settings.

**Working in monorepos:**

If your Python codebase is within a subfolder of a larger monorepo, VSCode offers a couple of approaches for proper configuration. You can either use VSCode's multi-root workspace feature to treat each subfolder as an independent project, or you can customize your workspace-level `.vscode/settings.json` to setup your Python code's location.

For exemple, assuming your python code is in a `server` subfolder:

```.vscode/settings.json
{
  "python.defaultInterpreterPath": "./server/.venv/bin/python",
  "ruff.configuration": "./server/pyproject.toml",
  "mypy-type-checker.cwd": "${workspaceFolder}/server",
  "mypy-type-checker.args": ["--config-file", "pyproject.toml"],
  "python.analysis.extraPaths": ["${workspaceFolder}/server"],
  "python.autoComplete.extraPaths": ["${workspaceFolder}/server"]
}
```

## 2. Typings
