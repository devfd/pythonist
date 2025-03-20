# Pythonist <!-- omit in toc -->

Your short, to the point, little guide for writing modern Python and improving on the outdated code LLMs seem to be stuck on.

- [1. Tooling](#1-tooling)
  - [1.1. Package Management with uv](#11-package-management-with-uv)
  - [1.2. Linting \& Formatting with Ruff](#12-linting--formatting-with-ruff)
  - [1.3. Setting up VSCode for Python](#13-setting-up-vscode-for-python)
- [2. Typings](#2-typings)

## 1. Tooling

### 1.1. Package Management with uv

Simply use [uv](https://docs.astral.sh/uv/): it's ultra fast and comes with many great features, like workspaces, or bytecode compilation to boost startup times.

To start using `uv` in your project, simply run:

```bash
uv init # create minimal pyproject.toml
```

**Dependency Groups & Development Dependencies:**

To maintain a clean and lightweight Docker image for deployment, install development related tools and dependencies (like linters, formatters, testing frameworks) using the --dev flag:

```bash
uv add --dev <dev-dependency-name>
```

### 1.2. Linting & Formatting with Ruff

Use [ruff](https://docs.astral.sh/ruff/): it's incredibly fast *and* combines the functionalities of Black (formatter), isort (import sorter), and Flake8 (linter) into a single tool.

Install Ruff as a development dependency using `uv`:

```bash
uv add --dev ruff # install ruff in the dev dependencies group
```

### 1.3. Setting up VSCode for Python

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

## 2. Typings
