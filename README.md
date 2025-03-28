# Pythonist <!-- omit in toc -->

Your short, to the point guide for writing modern Python code.

- [1. Developer Experience](#1-developer-experience)
  - [1.1. Package Management with uv](#11-package-management-with-uv)
  - [1.2. Linting \& Formatting with Ruff](#12-linting--formatting-with-ruff)
  - [1.3. Testing](#13-testing)
  - [1.4. Setting up VSCode](#14-setting-up-vscode)
- [2. Typings](#2-typings)
  - [2.1. Setup](#21-setup)
  - [2.2. Common types](#22-common-types)
  - [2.3. Object or Any](#23-object-or-any)
  - [2.4. Functions](#24-functions)
  - [2.5. Custom types](#25-custom-types)
  - [2.6. Keeping up-to-date](#26-keeping-up-to-date)

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

### 1.4. Setting up VSCode

Here are the minimal recommended extensions to have a great development experience:

- [Pylance](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance)
- [Ruff](https://marketplace.visualstudio.com/items?itemName=charliermarsh.ruff)
- [mypy](https://marketplace.visualstudio.com/items?itemName=ms-python.mypy-type-checker)

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

Typing is also essential and should be part of every Python project. It makes refactoring easier, catches errors early, and improves code readability for everyone (including LLMs!).

### 2.1. Setup

Add mypy to your project:

```bash
uv add --dev mypy
```

**Configuring mypy:**

To tweak mypy settings, add a section to your `pyproject.toml`:

```toml
[tool.mypy]
strict = true
strict_optional = true
warn_return_any = true
warn_unreachable = true
exclude = [".venv"]
```

Refer to [mypy documentation page](https://mypy.readthedocs.io/en/stable/config_file.html#example-pyproject-toml) for more info.

### 2.2. Common types

Python's type system includes built-in support for commonly used types. Here’s a quick overview:

- `str`: for strings.
- `int`: for integers.
- `float`: for floating-point numbers.
- `bool`: for booleans.
- `list[item_type]`: for lists containing items of item_type.
- `dict[key_type, value_type]`: for dictionaries.
- `tuple[type1, type2]`: for tuples of fixed size.
- `tuple[type1, ...]`: for tuples of variable size.
- `set[item_type]`: for sets.
- `type1 | type2:` union type, for when a value can be one of a few types.
- `type | None`: optional type, for a value that can be none.

```python
name: str = "Alice" # string
age: int = 30 # integer
crown_jewel_weight_kg: float = 4.2 # float
is_rebel: bool = True # boolean
royal_titles: list[str] = ["Princess of the forest", "Duchess of the sea"] # list of strings
known_languages: dict[str, str] = {"elvish": "fluent", "common": "native"} # dict
garden_dimensions: tuple[str, int, int] = ("METER", 36, 6) # fixed-size typle
favorite_numbers: tuple[int, ...] = (7, 13, 21, 34) # variable-size typle
castle_rooms: set[str] = {"throne_room", "ballroom", "library"} # set
knight_in_service: str | None = "Sir Reginald"  # either str or None
```

**Type inference:**

While MyPy can often infer types from assigned values, declaring types clearly, as shown in the example, greatly enhances code readability. It's a recommended practice for maintainable Python.

**Runtime behavior:**

Remember, type hints are primarily for static analysis by tools like MyPy and are **not enforced at runtime**.

You need an additional library to enforce types at runtime, like [typeguard](https://github.com/agronholm/typeguard).

### 2.3. Object or Any

You should avoid using `object` or `Any`. If for some reasons you need an escape hatch or types are truly unknown (or dynamic) consider:

- `object`:  Base class for all classes. Only universally supported operations are allowed, offering some type safety.
- `Any`: Effectively disables type checking. Any operations are permitted on the value.

```python
from typing import Any

# using object
vague_attribute: object = "Crown Jewel"
vague_attribute = 3.14 # Reassigning to a float - also valid with object
print(vague_attribute.upper()) # Static type error! 'object' has no attribute 'upper'
vague_attribute + 5 # Static type error!  Binary operation "+" not defined for 'object' and 'int'
print(vague_attribute.__str__()) # Okay - __str__ is a universal method for objects
print(type(vague_attribute)) # Okay - type() works for all objects

# using Any
flexible_attribute: Any = "Royal Decree"
flexible_attribute = 42  # Reassigning to an integer - perfectly valid with Any
print(flexible_attribute.upper()) # No type error initially, runtime error if type is not str
flexible_attribute + 10 # No type error, even if 'flexible_attribute' is a string later
```

### 2.4. Functions

In strict mode, mypy will enforce you to type both function arguments and return values.

Here is how you would type a function:

```python
def calculate_sum(numbers: list[int]) -> int:
    return sum(numbers)
```

**Explicit None Return Type:**

For functions that don't explicitly return a value, it's best practice to explicitly annotate the return type as None.

```python
def process_data(data: list[str]) -> None:
    # ... process the data ...
```

**Prefer immutable containers for arguments:**

When a function is designed not to modify its input arguments, using immutable container types in the function signature is highly recommended.

- `list[item_type]`: use `typing.Sequence[item_type]`, if you need random access via my_list[id] or `typing.Iterable[item_type]` if you are just iterating over the list.
- `dict[key_type, value_type]`: use `typing.Mapping[key_type, value_type]`, if you need random access via my_dict[key] or `typing.Iterable[tuple[key_type, value_type]]` if you are just iterating over the dictionary
- `set[item_type]`: use `typing.AbstractSet[item_type]`

**Type Narrowing with typing.TypeIs:**

A function returning a bool value can be used to narrow the type of a variable.

 `TypeIs` can narrow the type in both the if and else branches of a conditional.

From the Python [documentation](https://typing.python.org/en/latest/spec/narrowing.html#typeis):

```python
from typing import TypeIs, assert_type

def is_str(x: object) -> TypeIs[str]:
    return isinstance(x, str)

def f(x: str | int) -> None:
    if is_str(x):
        assert_type(x, str)
    else:
        assert_type(x, int)
```

### 2.5. Custom types

Python's typing system isn't limited to built-in types. You can define your own custom types for increased clarity and type safety.

**Litterals:**

Use Literal to restrict a variable to a specific set of literal values (strings, numbers, booleans). Great for configuration options or constrained inputs.

```python
from typing import Literal

def set_log_level(level: Literal["DEBUG", "INFO", "WARNING", "ERROR"]) -> None:
    if level == "DEBUG":
```

**Enums:**

For a more structured way to represent a fixed set of named values, use Enum from the enum module. Enums are more readable and maintainable than using plain strings or magic numbers.

```python
class UserRole(str, Enum): # Inherit from str for easy JSON serialization
    ADMIN = "admin"
    EDITOR = "editor"
    VIEWER = "viewer"

def grant_permission(role: UserRole) -> None:
    if role == UserRole.ADMIN:
```

**Classes:**

Classes themselves act as custom types. When you define a class, you're creating a new type. Use class names directly as type hints.

**Type alias:**

Create type aliases to give descriptive names to complex types, improving readability, especially for nested or repetitive type hints.

```python
type UserProfileData = Dict[str, List[Tuple[str, int]]]

user_data: UserProfileData = {
    "alice": [("login_count", 5), ("last_activity", 1678886400)],
    "bob": [("login_count", 12), ("last_activity", 1678972800)],
}
```

### 2.6. Keeping up-to-date

Typings are continuously evolving, with features introduced in each new Python release.

[The PEP (Python Enhancement Proposal) index for typing](https://peps.python.org/topic/typing/) is a great place to keep an eye on upcoming features and understand the rationale behind existing ones.
