# Python `ty` Type Checker — Complete Guide

**Level:** Beginner to Advanced  
**Language:** Python  
**Tool developer:** Astral  
**Purpose:** Detect type errors, identify suspicious code, and improve code reliability.

---

# 1. What Is `ty`?

`ty` is a Python type checker and language server written in Rust.

It analyzes Python source code and helps identify problems involving data types, function arguments, return values, attributes, and other type-related behavior.

For example:

```python
def add(a: int, b: int) -> int:
    return a + b


result = add(10, 20)
print(result)
```

This function declares that:
- `a` should be an integer.
- `b` should be an integer.
- The function should return an integer.

Now consider this:

```python
def add(a: int, b: int) -> int:
    return a + b


result = add("10", "20")
```

The arguments are strings, although the function declares them as integers.

A type checker can identify this mismatch before you run the program.

**Important:** Python type annotations generally do not enforce types at runtime by themselves. A tool such as `ty` analyzes the code to detect problems.

Official documentation: https://docs.astral.sh/ty/type-checking/

---

# 2. Understand the Astral Toolchain

Astral develops several tools that solve different problems.

| Tool | Main responsibility |
|---|---|
| `uv` | Python package management, project environments, dependency resolution, and running commands |
| `Ruff` | Linting and formatting Python code |
| `ty` | Static type checking |
| `pytest` | Running automated tests |
| `unittest` | Python's built-in testing framework |
| Docker | Packaging an application and its environment into containers |

These tools complement one another.

For example, consider:

```python
def divide(a: int, b: int) -> int:
    return a / b
```

Potential observations:

- `ty` can identify that `/` produces a floating-point result, which conflicts with the declared `int` return type.
- Ruff can identify configured linting problems and format the code.
- Tests can verify whether the function produces the results your application requires.
- Docker can package the application for deployment.

No single tool replaces all the others.

---

# 3. Install `ty`

If you manage your project with `uv`, you have several installation options.

## Option A: Run `ty` without installing it permanently

```bash
uvx ty check
```

`uvx` runs a tool in an isolated environment.

Use this when you want to try `ty` without adding it as a project dependency.

## Option B: Install `ty` as a project development dependency

From your project directory:

```bash
uv add --dev ty
```

This adds `ty` to your project's development dependencies.

Run it using:

```bash
uv run ty check
```

This is a good choice when you want your project to declare which development tools it uses.

## Option C: Install `ty` as a global command-line tool

```bash
uv tool install ty
```

Then run:

```bash
ty check
```

To upgrade the globally installed tool:

```bash
uv tool upgrade ty
```

To uninstall it:

```bash
uv tool uninstall ty
```

### Which option should you choose?

| Situation | Recommended command |
|---|---|
| Trying `ty` for the first time | `uvx ty check` |
| Using `ty` in a `uv`-managed project | `uv add --dev ty` |
| Wanting `ty` available globally | `uv tool install ty` |

Official installation guide: https://docs.astral.sh/ty/installation/

---

# 4. Run Your First Type Check

Suppose your project looks like this:

```text
my-project/
├── pyproject.toml
├── uv.lock
├── calculator.py
└── main.py
```

Inside `calculator.py`:

```python
def add(a: int, b: int) -> int:
    return a + b
```

Inside `main.py`:

```python
from calculator import add

result = add(10, 20)

print(result)
```

Run:

```bash
uv run ty check
```

`ty` checks Python files in the project.

You can also check a specific file:

```bash
uv run ty check main.py
```

Check a directory:

```bash
uv run ty check .
```

The `.` means the current directory.

You can find the available commands and options with:

```bash
uv run ty --help
uv run ty check --help
```

---

# 5. Understand Python Type Annotations First

To understand `ty`, you need to understand Python type annotations.

## 5.1 Variable annotations

```python
name: str = "Charles"

age: int = 25

price: float = 19.99

is_active: bool = True
```

The colon introduces a type annotation.

For example:

```python
name: str = "Charles"
```

This communicates that `name` is intended to hold a string.

Common annotations:

| Annotation | Meaning | Example |
|---|---|---|
| `str` | String | `"Python"` |
| `int` | Integer | `25` |
| `float` | Floating-point number | `19.99` |
| `bool` | Boolean | `True` |
| `bytes` | Immutable sequence of bytes | `b"hello"` |
| `None` | No return value or absence of a value | `None` |

An annotation communicates your intent. Python does not automatically reject every assignment that conflicts with it.

For example:

```python
age: int = "twenty"
```

Python can execute this assignment, but a static type checker can report the mismatch.

## 5.2 Function parameter annotations

```python
def greet(name: str) -> str:
    return f"Hello, {name}!"
```

Here:

```python
name: str
```

means the parameter is intended to be a string.

And:

```python
-> str
```

declares that the function returns a string.

Example:

```python
message = greet("Charles")
```

## 5.3 The meaning of `-> None`

Consider:

```python
def display_message(message: str) -> None:
    print(message)
```

The function performs an action but does not return a meaningful result.

Therefore, its return annotation is `None`.

Do not confuse printing a value with returning a value.

```python
def example() -> None:
    print("Hello")
```

This function prints text and implicitly returns `None`.

---

# 6. Common Type Errors

## Example 1: Incorrect argument type

```python
def multiply(a: int, b: int) -> int:
    return a * b


result = multiply("5", 3)
```

The first argument is a string rather than an integer.

A type checker can identify this mismatch.

Correct version:

```python
def multiply(a: int, b: int) -> int:
    return a * b


result = multiply(5, 3)
```

## Example 2: Incorrect return type

```python
def get_age() -> int:
    return "twenty"
```

The function declares an integer return type but returns a string.

Correct version:

```python
def get_age() -> int:
    return 20
```

## Example 3: Incorrect assignment

```python
username: str = 100
```

Correct version:

```python
username: str = "100"
```

Alternatively, if the value should be numeric:

```python
username: int = 100
```

Choose the correct type based on the meaning of the data.

## Example 4: Calling a method on the wrong type

```python
age: int = 25

age.append(30)
```

Integers do not provide a list's `append()` method.

Correct version:

```python
ages: list[int] = [25]

ages.append(30)
```

The type checker can identify many errors like these before execution.

---

# 7. Advanced Type Annotations

## 7.1 Lists

Use `list[Type]` to describe a list containing values of a particular type.

```python
names: list[str] = [
    "Alice",
    "Bob",
    "Charles",
]
```

A list of integers:

```python
numbers: list[int] = [10, 20, 30]
```

A function accepting a list of integers:

```python
def calculate_total(numbers: list[int]) -> int:
    return sum(numbers)
```

## 7.2 Dictionaries

Use `dict[KeyType, ValueType]`.

```python
user: dict[str, str] = {
    "username": "charles",
    "email": "charles@example.com",
}
```

A dictionary with string keys and integer values:

```python
scores: dict[str, int] = {
    "Alice": 90,
    "Bob": 85,
}
```

## 7.3 Tuples

Use `tuple` when the number and position of values matter.

```python
user: tuple[str, int] = ("Charles", 25)
```

This tuple contains:
- A string in the first position.
- An integer in the second position.

## 7.4 Optional values

Sometimes a value may be present or absent.

Use `str | None` to express this.

```python
username: str | None = None
```

Example:

```python
def find_username(user_id: int) -> str | None:
    if user_id == 1:
        return "charles"

    return None
```

Before using the result as a string, check whether it is `None`.

```python
username = find_username(1)

if username is not None:
    print(username.upper())
```

This is important because calling `.upper()` on `None` would fail at runtime.

## 7.5 Union types

A union means a value may have one of several types.

```python
identifier: int | str
```

It may contain an integer or a string.

For example:

```python
def display_identifier(identifier: int | str) -> None:
    print(identifier)
```

A union does not automatically make every operation valid for every member of the union.

For example, you cannot assume that every value of type `int | str` supports string-only methods.

## 7.6 Type aliases

A type alias gives a descriptive name to a type.

```python
UserID = int


def get_user(user_id: UserID) -> str:
    return f"User {user_id}"
```

For a more complex type:

```python
UserRecord = dict[str, str | int]
```

You can then use:

```python
user: UserRecord = {
    "username": "charles",
    "age": 25,
}
```

Type aliases can make large projects easier to understand.

---

# 8. Type Checking Does Not Replace Validation

Consider an API request that contains JSON:

```python
payload = {
    "age": "twenty-five"
}
```

You might annotate a function:

```python
def register_user(age: int) -> None:
    print(age)
```

However, type annotations do not automatically convert or validate the external JSON data.

External data can come from:
- HTTP requests.
- JSON files.
- Databases.
- Environment variables.
- Command-line arguments.
- User input.

Validate external data at runtime.

For example:

```python
def parse_age(value: object) -> int:
    if isinstance(value, int) and not isinstance(value, bool):
        return value

    raise ValueError("Age must be an integer")
```

This function performs an actual runtime check.

The `bool` exclusion is useful because `bool` is a subclass of `int` in Python.

**Best practice:** Use static type checking to find programming mistakes, and runtime validation to protect application boundaries.

---

# 9. Configure `ty` in `pyproject.toml`

For a `uv`-managed project, you can configure `ty` in your existing `pyproject.toml`.

Do not replace the entire file. Add the relevant configuration to your existing project configuration.

Example:

```toml
[tool.ty.environment]
# Set this to the Python version your project targets.
python-version = "3.13"

[tool.ty.rules]
# Example: report this rule as a warning instead of an error.
possibly-unresolved-reference = "warn"
```

The example uses Python 3.13. Change it to match your project's target version.

The `rules` section controls the severity of individual type-checking rules.

Common severity levels:

| Severity | Meaning |
|---|---|
| `"ignore"` | Disable the diagnostic rule. |
| `"warn"` | Report a warning. |
| `"error"` | Report an error. |

For example:

```toml
[tool.ty.rules]
possibly-unresolved-reference = "warn"
division-by-zero = "ignore"
```

Use rule names supported by your installed version of `ty`. Consult its current rule documentation before adding more.

Official configuration reference:

https://docs.astral.sh/ty/reference/configuration/

---

# 10. Useful `ty` Commands

| Command | Purpose |
|---|---|
| `uvx ty check` | Run the type checker without a permanent project installation |
| `uv run ty check` | Check the current project using its `uv` environment |
| `uv run ty check .` | Check the current directory |
| `uv run ty check main.py` | Check a specific file |
| `uv run ty check --watch` | Watch for changes and recheck affected files |
| `uv run ty version` | Display the installed version |
| `uv run ty help` | Display general command help |
| `uv run ty check --help` | Display type-checking options |

Watch mode is especially useful during development:

```bash
uv run ty check --watch
```

As you modify your code, `ty` can recheck affected files.

Check the help output for your installed version if an option behaves differently.

---

# 11. Using `ty` Alongside Ruff and pytest

For a project managed by `uv`, you can install your development tools together:

```bash
uv add --dev ruff ty pytest
```

Then run:

```bash
uv run ruff check .
uv run ruff format --check .
uv run ty check
uv run pytest
```

Each command has a different purpose.

### Ruff

```bash
uv run ruff check .
```

Checks code for configured linting problems.

```bash
uv run ruff format .
```

Formats Python source files.

### ty

```bash
uv run ty check
```

Checks type-related problems.

### pytest

```bash
uv run pytest
```

Executes your automated tests.

**Important:** Passing a type check does not prove that your program is correct. Likewise, passing tests does not prove that every possible input is safe.

Use all these tools as complementary checks.

---

# 12. Example Project Workflow

Suppose you are building a Python application.

A practical development workflow is:

## Step 1: Create the project

```bash
uv init my-project
cd my-project
```

If you already have a project, do not initialize another one unnecessarily.

## Step 2: Add development tools

```bash
uv add --dev ruff ty pytest
```

## Step 3: Write code

Create or edit your Python modules.

## Step 4: Run Ruff

```bash
uv run ruff check .
```

## Step 5: Format your code

```bash
uv run ruff format .
```

## Step 6: Check types

```bash
uv run ty check
```

## Step 7: Run tests

```bash
uv run pytest
```

## Step 8: Review your changes

Fix reported issues, then rerun the relevant checks.

This workflow helps you identify different classes of problems before deployment.

---

# 13. Using `ty` with Django

You can also use `ty` in your Django project.

Install it as a development dependency:

```bash
uv add --dev ty
```

Then run:

```bash
uv run ty check
```

For example, consider this function:

```python
def calculate_discount(price: float, percentage: float) -> float:
    return price * percentage / 100
```

The declared parameter and return types communicate the intended interface.

You could call it as follows:

```python
discount = calculate_discount(100.0, 10.0)
```

A mismatched call such as:

```python
discount = calculate_discount("100", 10.0)
```

can be reported by the type checker.

### Django-specific consideration

Django uses dynamic patterns, including model attributes, managers, QuerySets, and framework-provided behavior.

Some framework behavior may require additional type information or configuration before a static type checker can understand it fully.

If `ty` reports an unresolved import or attribute in a Django project, do not immediately assume that your application is broken.

First check:
1. Whether you are running `ty` through the correct environment.
2. Whether the relevant dependencies are installed.
3. Whether the code actually has a type error.
4. Whether the framework behavior is supported by the type checker's available type information.

You can read the official guidance on resolving imports:

https://docs.astral.sh/ty/reference/typing-faq/

---

# 14. Suppressing a Diagnostic

Sometimes a type checker reports a problem that you have deliberately accepted.

Use suppressions sparingly and document why they are necessary.

For example, `ty` supports rule-specific suppression comments. A suppression generally has the form:

```python
value = some_expression()  # ty: ignore[rule-name]
```

Replace `rule-name` with the actual rule reported by `ty`.

Do not copy a made-up rule name and assume it will work.

Before suppressing a diagnostic, ask:
- Is the report correct?
- Can I fix the type annotation?
- Can I validate the input?
- Can I restructure the code?
- Is the suppression necessary?

A suppression should not be used to hide a genuine programming error.

---

# 15. Best Practices

1. **Use meaningful annotations.** Prefer `user_id: int` over an ambiguous annotation that communicates nothing useful.

2. **Annotate public functions.** Function parameters and return values are particularly useful places for type annotations.

3. **Use `None` deliberately.** If a function can return no value, communicate that with an appropriate return annotation.

4. **Validate external data.** Type annotations do not validate HTTP requests, JSON, environment variables, or database data at runtime.

5. **Use the correct environment.** For `uv` projects, `uv run ty check` is a good default.

6. **Fix genuine type errors.** Do not routinely suppress diagnostics just to obtain a clean report.

7. **Combine complementary tools.** Use Ruff, `ty`, and tests for different kinds of checks.

8. **Configure the target Python version correctly.** The type checker should analyze code against the Python version your project supports.

9. **Keep development tools reproducible.** Declaring tools in `pyproject.toml` and committing `uv.lock` helps other developers use the same dependency versions.

10. **Treat `ty` as an evolving tool.** Check the official documentation for the capabilities and configuration supported by your installed version.

---

# 16. Quick Reference

```bash
# Try ty without installing it permanently.
uvx ty check

# Add ty to a uv-managed project's development dependencies.
uv add --dev ty

# Check the project.
uv run ty check

# Check one file.
uv run ty check main.py

# Watch for code changes.
uv run ty check --watch

# Display the installed version.
uv run ty version

# Display help.
uv run ty check --help

# Run linting.
uv run ruff check .

# Format code.
uv run ruff format .

# Run automated tests.
uv run pytest
```

---

# 17. Official Resources

- `ty` documentation: https://docs.astral.sh/ty/
- Installation: https://docs.astral.sh/ty/installation/
- Type checking: https://docs.astral.sh/ty/type-checking/
- Configuration: https://docs.astral.sh/ty/reference/configuration/
- Command-line reference: https://docs.astral.sh/ty/reference/cli/
- Typing FAQ: https://docs.astral.sh/ty/reference/typing-faq/

---

## Final Summary

Think of the tools this way:

- `uv` manages the Python environment and dependencies.
- Ruff checks code quality and formats code.
- `ty` checks whether your code's use of types is consistent.
- `pytest` executes your tests.
- Docker packages your application and its runtime environment.

Together, these tools provide a strong foundation for building maintainable Python applications, including Django APIs and cybersecurity projects.
