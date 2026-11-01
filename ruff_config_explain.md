# Ruff Configuration Guide: `pyproject.toml` and `ruff.toml`

A complete guide to configuring Ruff for Python, Django, and other Python projects.

## Table of Contents

1. What is Ruff configuration?
2. The two configuration-file formats
3. Understanding TOML syntax
4. Main Ruff configuration settings
5. Configuring lint rules
6. Configuring the formatter
7. Excluding files and directories
8. Configuring different rules for different files
9. Extending another Ruff configuration
10. Using command-line overrides
11. Checking your active configuration
12. Complete `pyproject.toml` example
13. Complete `ruff.toml` example
14. Recommended Django configuration
15. Common mistakes
16. Quick reference

---

# 1. What Is Ruff Configuration?

Ruff is a Python tool that provides two major features:

- **Linting:** Detects selected code-quality issues and potential programming mistakes.
- **Formatting:** Automatically standardizes the appearance of Python code.

Configuration allows you to control how Ruff performs these tasks.

For example, you can configure Ruff to:

- Use a line length of 88 characters.
- Target Python 3.13.
- Detect unused imports.
- Sort imports automatically.
- Enable additional bug-detection rules.
- Use single quotes when formatting strings.
- Ignore selected rules in test files.
- Exclude generated files and directories.
- Format code inside docstrings.

Without a custom configuration, Ruff uses its defaults.

A configuration file lets you customize those defaults for your project.

---

# 2. The Two Configuration-File Formats

Ruff supports three filenames:

1. `pyproject.toml`
2. `ruff.toml`
3. `.ruff.toml`

This guide focuses on the first two. The third is another Ruff-specific configuration filename.

## 2.1 Option A: `pyproject.toml`

This file can contain project metadata, dependencies, and settings for multiple development tools.

Example:

```toml
[project]
name = "django-store"
version = "0.1.0"
requires-python = ">=3.13"

[dependency-groups]
dev = [
    "ruff",
]

[tool.ruff]
line-length = 88
target-version = "py313"

[tool.ruff.lint]
select = ["E", "F", "I"]
```

Notice the section names:

```toml
[tool.ruff]
```

```toml
[tool.ruff.lint]
```

```toml
[tool.ruff.format]
```

The `tool.ruff` prefix identifies Ruff's configuration inside `pyproject.toml`.

**Use this format when:** You already manage your Python project using `pyproject.toml`, especially with `uv`.

## 2.2 Option B: `ruff.toml`

This file is dedicated to Ruff configuration.

Equivalent example:

```toml
line-length = 88
target-version = "py313"

[lint]
select = ["E", "F", "I"]
```

Notice that the section names are shorter.

In `ruff.toml`, you write:

```toml
[lint]
```

rather than:

```toml
[tool.ruff.lint]
```

Similarly, the formatter section is:

```toml
[format]
```

rather than:

```toml
[tool.ruff.format]
```

**Use this format when:** You want to keep Ruff configuration separate from your project's other settings.

## 2.3 Are the settings different?

No. The settings are essentially the same; the main difference is their TOML structure.

| Purpose | `pyproject.toml` | `ruff.toml` |
|---|---|---|
| Main settings | `[tool.ruff]` | Top level |
| Lint settings | `[tool.ruff.lint]` | `[lint]` |
| Formatter settings | `[tool.ruff.format]` | `[format]` |
| Per-file lint ignores | `[tool.ruff.lint.per-file-ignores]` | `[lint.per-file-ignores]` |
| Import sorting settings | `[tool.ruff.lint.isort]` | `[lint.isort]` |
| Docstring lint settings | `[tool.ruff.lint.pydocstyle]` | `[lint.pydocstyle]` |

Official reference: Ruff configuration documentation.

---

# 3. Understanding TOML Syntax

Before extending Ruff's configuration, understand how TOML works.

TOML is a configuration-file format used by Python tools, including Ruff and `uv`.

## 3.1 Key-value pairs

Example:

```toml
line-length = 88
```

This assigns the integer value `88` to the setting `line-length`.

Another example:

```toml
target-version = "py313"
```

Here, the value is a string.

## 3.2 Strings

Strings use quotation marks:

```toml
quote-style = "double"
```

This configures Ruff's formatter to use double quotes where appropriate.

## 3.3 Lists

Lists use square brackets:

```toml
select = ["E", "F", "I"]
```

This enables the selected lint-rule groups.

You can also write the list over multiple lines:

```toml
select = [
    "E",
    "F",
    "I",
    "B",
    "UP",
]
```

Both forms are valid TOML.

## 3.4 Sections

A section groups related settings.

```toml
[tool.ruff.lint]
select = ["E", "F"]
ignore = ["E501"]
```

The settings belong to Ruff's linting configuration.

## 3.5 Comments

Use `#` for comments:

```toml
# Maximum line length for formatting and applicable lint rules.
line-length = 88
```

Comments help you remember why a setting exists.

---

# 4. Main Ruff Configuration Settings

These settings belong directly under `[tool.ruff]` in `pyproject.toml`, or at the top level of `ruff.toml`.

## 4.1 `line-length`

Example:

```toml
line-length = 88
```

**Purpose:** Sets the preferred maximum line length for Ruff's formatter and applicable lint rules.

For example:

```python
message = "This is a Python message."
```

You can increase the limit:

```toml
line-length = 100
```

Or decrease it:

```toml
line-length = 79
```

A common starting point is 88 characters.

Remember that a formatter may sometimes keep a line longer when restructuring it would not be appropriate.

## 4.2 `indent-width`

Example:

```toml
indent-width = 4
```

**Purpose:** Sets the configured indentation width.

For Python, four spaces is the conventional indentation width.

Example:

```python
def greet():
    print("Hello")
```

The indentation width also affects formatting behavior for supported file types and constructs.

## 4.3 `target-version`

Example:

```toml
target-version = "py313"
```

**Purpose:** Tells Ruff which Python version your project targets.

Common values include:

| Value | Python version |
|---|---|
| `py310` | Python 3.10 |
| `py311` | Python 3.11 |
| `py312` | Python 3.12 |
| `py313` | Python 3.13 |
| `py314` | Python 3.14 |

Use the version that matches your project's compatibility requirements.

Do not automatically select the newest version simply because it is installed on your computer.

This setting affects which syntax and code transformations Ruff considers appropriate.

## 4.4 `exclude`

Example:

```toml
exclude = [
    ".git",
    ".venv",
    "__pycache__",
    "build",
    "dist",
]
```

**Purpose:** Specifies file and directory patterns Ruff should exclude from normal discovery.

This is useful for generated files, virtual environments, and build artifacts.

Ruff already excludes several common directories by default, so you do not need to repeat every default exclusion.

## 4.5 `extend-exclude`

Example:

```toml
extend-exclude = [
    "generated",
    "legacy_code",
]
```

**Purpose:** Adds additional exclusions to Ruff's default exclusion patterns.

The difference is:

- `exclude` defines the exclusion patterns for the configuration.
- `extend-exclude` adds extra exclusions while retaining the default exclusions.

Use `extend-exclude` when you only want to add more directories to ignore.

## 4.6 `include`

Example:

```toml
include = [
    "*.py",
    "*.pyi",
    "src/**/*.py",
    "scripts/**/*.py",
]
```

**Purpose:** Defines which file patterns Ruff should discover.

Be careful: `include` is not simply an additional list of files. It changes the discovery patterns, so an overly restrictive setting may prevent Ruff from finding files you expected it to check.

Use it only when you need more control over file discovery.

## 4.7 `extend-include`

Example:

```toml
extend-include = [
    "*.pyw",
]
```

**Purpose:** Adds file patterns to Ruff's default inclusion patterns.

Use this when you want Ruff to discover additional supported file types without replacing its default inclusions.

## 4.8 `force-exclude`

Example:

```toml
force-exclude = true
```

**Purpose:** Enforces exclusion patterns even when files are explicitly passed to Ruff.

For example, suppose `generated/example.py` is excluded.

Without forced exclusions, explicitly passing an excluded file may still cause Ruff to analyze it.

With `force-exclude = true`, the exclusion is enforced for explicitly supplied paths too.

## 4.9 `src`

Example:

```toml
src = [
    "src",
    "tests",
]
```

**Purpose:** Identifies directories that Ruff should treat as source roots for import classification.

This can help Ruff distinguish first-party imports from third-party imports.

For a project using a `src` layout, you might configure:

```toml
src = ["src"]
```

Do not add directories blindly. Choose the actual source directories used by your project.

## 4.10 `preview`

Example:

```toml
preview = false
```

**Purpose:** Controls Ruff's preview features.

Preview mode can enable rules, fixes, and other behavior that is not yet considered stable.

A good starting point is to leave preview mode disabled unless you specifically want to experiment with preview functionality.

## 4.11 `required-version`

Example:

```toml
required-version = ">=0.14.0"
```

**Purpose:** Specifies a Ruff version requirement.

The value must use a valid version requirement supported by Ruff's configuration syntax.

This is useful when your project depends on behavior available only in certain Ruff releases.

**Important:** The example is illustrative. Choose a version requirement that matches the Ruff release your project actually needs.

## 4.12 `extend`

Example:

```toml
extend = "../ruff.toml"
```

**Purpose:** Inherits configuration from another Ruff configuration file.

This is particularly useful when several projects share a common configuration.

We will examine it in detail in Section 9.

---

# 5. Configuring Lint Rules

Lint rules control which code-quality issues Ruff detects.

In `pyproject.toml`, use:

```toml
[tool.ruff.lint]
```

In `ruff.toml`, use:

```toml
[lint]
```

## 5.1 `select`

Example:

```toml
[tool.ruff.lint]
select = [
    "E",
    "F",
    "I",
    "B",
    "UP",
]
```

**Purpose:** Specifies the lint rules to enable.

Common rule prefixes include:

| Prefix | Purpose |
|---|---|
| `E` | Selected style-related checks |
| `F` | Pyflakes checks, including unused imports and undefined names |
| `W` | Selected warning-style checks |
| `I` | Import sorting |
| `B` | Bugbear checks for potential bugs and questionable patterns |
| `UP` | Modern Python syntax suggestions |
| `SIM` | Simplification suggestions |
| `N` | Naming conventions |
| `D` | Docstring conventions |
| `S` | Selected security-oriented checks from Ruff's flake8-bandit rules |
| `PT` | Pytest-related checks |
| `RET` | Return statement checks |
| `ARG` | Unused function argument checks |

Each prefix represents a family of rules. Enabling a family may produce findings that require review.

For example:

```toml
select = ["F"]
```

enables the `F` rules rather than the full set of rule families in the earlier example.

## 5.2 `extend-select`

Example:

```toml
[tool.ruff.lint]
extend-select = [
    "B",
    "SIM",
]
```

**Purpose:** Adds rule families to Ruff's existing selection.

The difference:

```toml
select = ["E", "F"]
```

sets the selection to the specified rules.

Meanwhile:

```toml
extend-select = ["B"]
```

extends the existing selection with additional rules.

Use `extend-select` when you want to enable more rules without replacing the existing selection.

## 5.3 `ignore`

Example:

```toml
[tool.ruff.lint]
ignore = [
    "E501",
]
```

**Purpose:** Disables selected rules.

`E501` concerns line length.

However, Ruff's formatter handles line wrapping, and `E501` is not enabled by default in Ruff's standard lint selection. You generally do not need to add this ignore unless you have enabled the rule or inherited a configuration that enables it.

Avoid ignoring rules simply to silence warnings without understanding them.

## 5.4 `extend-ignore`

Example:

```toml
[tool.ruff.lint]
extend-ignore = [
    "E501",
]
```

**Purpose:** Adds rules to the existing ignore list.

Use this when extending an existing configuration and wanting to ignore an additional rule without replacing its current ignore list.

## 5.5 `fixable`

Example:

```toml
[tool.ruff.lint]
fixable = [
    "F401",
    "I",
]
```

**Purpose:** Restricts which rule codes Ruff is permitted to fix automatically.

Here:

- `F401` concerns unused imports.
- `I` selects the import-sorting rule family.

The setting controls automatic fixing; it does not enable these rules by itself.

For example, a rule must also be selected before Ruff can report its violations.

## 5.6 `unfixable`

Example:

```toml
[tool.ruff.lint]
unfixable = [
    "B",
]
```

**Purpose:** Prevents Ruff from automatically fixing the specified rule codes.

This is useful when you want Ruff to report certain findings but require a human to make the changes.

The rules remain available for linting if they are selected.

## 5.7 `per-file-ignores`

Example:

```toml
[tool.ruff.lint.per-file-ignores]
"__init__.py" = ["F401"]
"tests/*.py" = ["S101"]
```

**Purpose:** Ignores selected rules in particular files or matching paths.

For example, `__init__.py` files sometimes import objects to expose them through the package API. An import may be intentional even when it appears unused.

Similarly, `S101` flags the use of `assert` under Ruff's security-oriented rule family. Assertions are commonly used in tests, so a project may choose to ignore that rule for test files.

The pattern `"tests/*.py"` matches according to Ruff's path-pattern semantics. For deeply nested test directories, you may need a recursive pattern such as `"**/tests/**/*.py"`.

## 5.8 `extend-per-file-ignores`

Example:

```toml
[tool.ruff.lint.extend-per-file-ignores]
"__init__.py" = ["F401"]
```

**Purpose:** Adds file-specific ignored rules to an existing per-file ignore configuration.

Use it when inheriting another configuration and wanting to add exceptions without replacing existing exceptions.

## 5.9 `dummy-variable-rgx`

Example:

```toml
[tool.ruff.lint]
dummy-variable-rgx = "^(_+|unused_.*)$"
```

**Purpose:** Defines a regular expression for variable names that Ruff should recognize as intentionally unused in applicable rules.

For example:

```python
for unused_item in range(3):
    print("Hello")
```

The configured expression recognizes names beginning with `unused_` as intentional placeholders for applicable checks.

This is especially useful when a function or loop must accept a value that is not needed.

## 5.10 `task-tags`

Example:

```toml
[tool.ruff.lint]
task-tags = [
    "TODO",
    "FIXME",
    "NOTE",
]
```

**Purpose:** Configures task markers used by Ruff's relevant comment-related rules.

For example:

```python
# TODO: Add validation.
```

This setting is useful for projects that use consistent task labels in comments.

## 5.11 Understanding rule codes

You can inspect a rule with:

```bash
uv run ruff rule F401
```

You can inspect the available configuration options with:

```bash
uv run ruff config
```

You can also query a particular setting:

```bash
uv run ruff config line-length
```

The configuration command is useful when you want to discover additional options supported by your installed Ruff version.

---

# 6. Configuring the Formatter

The formatter controls how Ruff rewrites your code's appearance.

In `pyproject.toml`:

```toml
[tool.ruff.format]
```

In `ruff.toml`:

```toml
[format]
```

## 6.1 `quote-style`

Example:

```toml
[tool.ruff.format]
quote-style = "double"
```

**Purpose:** Chooses the preferred quotation style for strings.

Available choices:

```toml
quote-style = "double"
```

or:

```toml
quote-style = "single"
```

For example, with the single-quote setting:

```python
message = 'Hello, Python!'
```

Ruff generally respects situations where changing the quote type would introduce additional escaping or be otherwise undesirable.

## 6.2 `indent-style`

Example:

```toml
[tool.ruff.format]
indent-style = "space"
```

**Purpose:** Selects the indentation style.

Supported values:

```toml
indent-style = "space"
```

or:

```toml
indent-style = "tab"
```

Spaces are the usual choice for Python code.

## 6.3 `line-ending`

Example:

```toml
[tool.ruff.format]
line-ending = "lf"
```

**Purpose:** Configures the line-ending convention used by the formatter.

Common options include:

| Value | Meaning |
|---|---|
| `"lf"` | Line feed, commonly used on Linux and macOS |
| `"cr-lf"` | Carriage return followed by line feed, common on Windows |
| `"native"` | Use the operating system's native line-ending convention |
| `"auto"` | Detect the existing convention where applicable |

For projects shared across operating systems, agree on a consistent line-ending convention.

## 6.4 `skip-magic-trailing-comma`

Example:

```toml
[tool.ruff.format]
skip-magic-trailing-comma = false
```

**Purpose:** Controls whether a trailing comma influences Ruff's multiline formatting decisions.

For example:

```python
items = [
    "Python",
    "Django",
]
```

A trailing comma can encourage multiline formatting in certain expressions.

Setting this option to `true` tells Ruff to ignore that particular formatting signal.

The default is generally suitable for most projects.

## 6.5 `docstring-code-format`

Example:

```toml
[tool.ruff.format]
docstring-code-format = true
```

**Purpose:** Enables formatting of supported code examples inside docstrings.

For example:

```python
def greet(name):
    """Return a greeting.

    Example:
        greet("Nzegge")
    """
    return f"Hello, {name}"
```

When this option is enabled, Ruff can format supported Python code examples embedded in docstrings.

## 6.6 `docstring-code-line-length`

Example:

```toml
[tool.ruff.format]
docstring-code-line-length = "dynamic"
```

**Purpose:** Controls the line length used for supported code examples in docstrings.

The `"dynamic"` setting allows Ruff to derive the applicable line length from the surrounding context.

You can instead specify an integer:

```toml
docstring-code-line-length = 60
```

Use this if you want docstring code examples to follow a particular maximum line length.

---

# 7. Excluding Files and Directories

Exclusions are useful because not every file in a project should necessarily be linted or formatted.

Suppose your Django project contains:

```text
django_store/
├── manage.py
├── config/
├── products/
├── generated/
├── .venv/
├── build/
└── dist/
```

You generally want Ruff to check your application code, not generated files or virtual-environment contents.

## Example in pyproject.toml

```toml
[tool.ruff]
extend-exclude = [
    "generated",
    "build",
    "dist",
]
```

## Equivalent ruff.toml

```toml
extend-exclude = [
    "generated",
    "build",
    "dist",
]
```

### What happens?

- `generated` adds a directory to the exclusions.
- `build` excludes build artifacts.
- `dist` excludes distribution artifacts.

The virtual environment and several common generated directories are already excluded by default.

### Check a project

Run:

```bash
uv run ruff check .
```

If you need to investigate which files are being processed, use:

```bash
uv run ruff check . --verbose
```

For file exclusions, remember that pattern matching and the directory from which Ruff resolves paths matter.

---

# 8. Configuring Different Rules for Different Files

Sometimes the same lint rule should be applied differently to application code and test code.

For example, tests often use `assert`, while application code may have different requirements.

## Example in pyproject.toml

```toml
[tool.ruff.lint]
select = [
    "E",
    "F",
    "I",
    "S",
]

[tool.ruff.lint.per-file-ignores]
"tests/*.py" = ["S101"]
```

## Equivalent ruff.toml

```toml
[lint]
select = [
    "E",
    "F",
    "I",
    "S",
]

[lint.per-file-ignores]
"tests/*.py" = ["S101"]
```

### Understanding the configuration

The `select` list enables the specified lint-rule families.

The per-file ignore says that the `S101` rule should not be applied to files matching the test pattern.

This does not disable every security-oriented rule in tests. It ignores only the specified rule.

For deeply nested test folders, adjust the path pattern to match your layout.

**Best practice:** Use narrow, intentional exceptions rather than disabling entire rule families unnecessarily.

---

# 9. Extending Another Ruff Configuration

This is an important feature.

Suppose you have a shared Ruff configuration:

```text
projects/
├── shared/
│   └── ruff.toml
├── django_store/
│   └── ruff.toml
└── security_tool/
    └── ruff.toml
```

You want the Django project and security tool to share common settings while allowing each project to customize its own configuration.

Ruff provides the `extend` setting for this purpose.

## 9.1 Shared configuration

File:

```text
projects/shared/ruff.toml
```

Contents:

```toml
line-length = 88
target-version = "py313"

[lint]
select = [
    "E",
    "F",
    "I",
]
```

This establishes the shared defaults for the projects that extend it.

## 9.2 Extend it in the Django project

File:

```text
projects/django_store/ruff.toml
```

Contents:

```toml
extend = "../shared/ruff.toml"

line-length = 100

[lint]
extend-select = [
    "B",
    "UP",
]
```

### What does each setting do?

```toml
extend = "../shared/ruff.toml"
```

Loads the shared configuration as the base.

```toml
line-length = 100
```

Overrides the inherited line length of 88 with 100.

```toml
[lint]
extend-select = ["B", "UP"]
```

Adds the `B` and `UP` rule families to the inherited lint selection.

The resulting configuration inherits the shared settings while applying the local overrides.

## 9.3 Can pyproject.toml extend ruff.toml?

Yes.

For example:

```toml
[tool.ruff]
extend = "../shared/ruff.toml"

line-length = 100

[tool.ruff.lint]
extend-select = ["B", "UP"]
```

The `extend` path must point to an existing, supported Ruff configuration file.

## 9.4 Important configuration-discovery rule

Ruff does not automatically merge every configuration file it finds in parent directories.

For a file, Ruff normally uses the closest applicable configuration file. Parent configurations are not automatically merged.

Use `extend` when you explicitly want to inherit settings from another configuration.

If multiple supported Ruff configuration filenames exist in the same directory, precedence is:

1. `.ruff.toml`
2. `ruff.toml`
3. `pyproject.toml`

For clarity, I recommend keeping just one primary Ruff configuration file in a project directory.

---

# 10. Using Command-Line Overrides

You can temporarily override configuration settings without editing the configuration file.

## 10.1 Override line length

```bash
uv run ruff format . --line-length 100
```

This formats the project using a line length of 100 for that invocation.

It does not permanently modify `pyproject.toml`.

## 10.2 Use a particular configuration file

```bash
uv run ruff check . --config ruff.toml
```

This explicitly tells Ruff which configuration file to use.

For a `pyproject.toml` file:

```bash
uv run ruff check . --config pyproject.toml
```

## 10.3 Override a setting directly

```bash
uv run ruff check . --config "line-length = 100"
```

This uses a configuration override for that command.

For formatter-specific settings, use the relevant setting path:

```bash
uv run ruff format . --config "format.quote-style = 'single'"
```

Be aware that command-line overrides take precedence over file configuration.

---

# 11. Checking Your Active Configuration

When Ruff behaves unexpectedly, investigate the settings it is actually using.

## 11.1 Display the settings for a file

```bash
uv run ruff check manage.py --show-settings
```

This displays the resolved settings for `manage.py`.

It is useful for investigating:

- Which lint rules are active.
- What line length is configured.
- Which exclusions apply.
- Which other settings affect the file.

## 11.2 List configuration options

```bash
uv run ruff config
```

This lists available configuration options for the installed Ruff version.

To inspect a specific option:

```bash
uv run ruff config line-length
```

For lint configuration:

```bash
uv run ruff config lint.select
```

For formatter configuration:

```bash
uv run ruff config format.quote-style
```

## 11.3 Check Ruff's version

```bash
uv run ruff --version
```

This helps you identify which Ruff version is being executed.

Some settings are version-dependent, so check your installed version when a configuration option is rejected.

---

# 12. Complete pyproject.toml Example

Here is a practical starting configuration for a Python project managed by `uv`.

```toml
[project]
name = "django-store"
version = "0.1.0"
requires-python = ">=3.13"

[dependency-groups]
dev = [
    "ruff",
]

[tool.ruff]
# Preferred maximum line length.
line-length = 88

# Python version targeted by the project.
target-version = "py313"

# Add exclusions to Ruff's defaults.
extend-exclude = [
    "generated",
    "build",
    "dist",
]

[tool.ruff.lint]
# Enable selected lint-rule families.
select = [
    "E",
    "F",
    "I",
    "B",
    "UP",
]

# Add any additional rule exceptions deliberately.
# Example:
# ignore = ["E501"]

[tool.ruff.lint.per-file-ignores]
# Allow unused imports in package initializer files
# when they are intentionally exposed as public names.
"__init__.py" = ["F401"]

[tool.ruff.format]
# Use consistent indentation.
indent-style = "space"

# Prefer double quotes.
quote-style = "double"

# Use a consistent line-ending convention.
line-ending = "lf"

# Format supported code examples in docstrings.
docstring-code-format = true
```

### What should you customize?

- Change `requires-python` to match your supported Python versions.
- Change `target-version` to the Python version you target.
- Add or remove lint-rule families according to your project's needs.
- Keep file-specific ignores as narrow as possible.
- Add formatting settings only when you want to change the defaults.

**Note:** If your existing `pyproject.toml` already contains project metadata or dependency declarations, merge these Ruff sections into it. Do not replace the entire file with this example.

---

# 13. Complete ruff.toml Example

Here is the equivalent standalone configuration.

```toml
# Preferred maximum line length.
line-length = 88

# Python version targeted by the project.
target-version = "py313"

# Add exclusions to Ruff's defaults.
extend-exclude = [
    "generated",
    "build",
    "dist",
]

[lint]
# Enable selected lint-rule families.
select = [
    "E",
    "F",
    "I",
    "B",
    "UP",
]

[lint.per-file-ignores]
# Allow intentionally unused imports in package initializer files.
"__init__.py" = ["F401"]

[format]
# Use consistent indentation.
indent-style = "space"

# Prefer double quotes.
quote-style = "double"

# Use a consistent line-ending convention.
line-ending = "lf"

# Format supported code examples in docstrings.
docstring-code-format = true
```

Notice that the configuration does not contain:

```toml
[tool.ruff]
```

It also does not contain:

```toml
[tool.ruff.lint]
```

That is because `ruff.toml` uses the shorter section names.

Choose one format for your project rather than keeping identical settings in both files.

---

# 14. Recommended Django Configuration

For a Django project managed by `uv`, I recommend beginning with a simple configuration and extending it as you learn which rules are useful.

## Option A: pyproject.toml

```toml
[tool.ruff]
line-length = 88
target-version = "py313"

[tool.ruff.lint]
select = [
    "E",
    "F",
    "I",
    "B",
    "UP",
]

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
```

## Install Ruff

From the directory containing `pyproject.toml`, run:

```bash
uv add --dev ruff
```

## Check the Django project

```bash
uv run ruff check .
```

## Fix supported linting problems

```bash
uv run ruff check . --fix
```

## Format the project

```bash
uv run ruff format .
```

## Check formatting without changing files

```bash
uv run ruff format --check .
```

## Run Django's own checks

```bash
uv run python manage.py check
```

## Run Django tests

```bash
uv run python manage.py test
```

Remember that these tools have different purposes:

- Ruff checks selected code-quality rules and formatting.
- Django's `check` command checks Django configuration and application setup.
- Django tests help verify application behavior.

---

# 15. Common Mistakes

## Mistake 1: Using the wrong section name

Incorrect in `pyproject.toml`:

```toml
[lint]
select = ["E", "F"]
```

Correct:

```toml
[tool.ruff.lint]
select = ["E", "F"]
```

The shorter `[lint]` section belongs in `ruff.toml`.

## Mistake 2: Putting the Ruff prefix in ruff.toml

Incorrect:

```toml
[tool.ruff.lint]
select = ["E", "F"]
```

Correct:

```toml
[lint]
select = ["E", "F"]
```

## Mistake 3: Thinking `extend-select` replaces `select`

This:

```toml
[tool.ruff.lint]
select = ["E", "F"]
extend-select = ["B"]
```

enables the selected `E` and `F` families and adds `B`.

## Mistake 4: Ignoring too many rules

This configuration may hide important findings:

```toml
[tool.ruff.lint]
ignore = ["E", "F", "B"]
```

Instead, identify the specific rule that is causing an intentional exception and ignore only that rule when justified.

## Mistake 5: Expecting parent configurations to merge automatically

Ruff does not automatically merge all configuration files in parent directories.

Use:

```toml
extend = "../ruff.toml"
```

when you want to inherit another configuration.

## Mistake 6: Assuming the configuration is always read from the current directory

Ruff discovers configuration according to the files being analyzed and the configuration hierarchy.

If you suspect the wrong configuration is being used, specify it explicitly:

```bash
uv run ruff check . --config ruff.toml
```

Then inspect the resolved settings:

```bash
uv run ruff check manage.py --show-settings
```

## Mistake 7: Assuming Ruff tests your application's logic

Ruff does not replace unit tests, integration tests, or Django's checks.

A clean Ruff report does not guarantee that your API endpoints, database operations, or authentication logic work correctly.

---

# 16. Quick Reference

| Setting | Purpose |
|---|---|
| `line-length` | Preferred maximum line length |
| `indent-width` | Configured indentation width |
| `target-version` | Python version targeted by Ruff |
| `exclude` | Exclusion patterns |
| `extend-exclude` | Additional exclusion patterns |
| `include` | File patterns Ruff should discover |
| `extend-include` | Additional file patterns |
| `force-exclude` | Enforce exclusions on explicitly supplied paths |
| `src` | Source roots used for import classification |
| `preview` | Enables preview functionality |
| `required-version` | Ruff version requirement |
| `extend` | Inherits another configuration |
| `select` | Selects lint rules |
| `extend-select` | Adds lint rules to the selection |
| `ignore` | Ignores specified rules |
| `extend-ignore` | Adds ignored rules |
| `fixable` | Restricts which rules can be automatically fixed |
| `unfixable` | Prevents automatic fixes for specified rules |
| `per-file-ignores` | Ignores selected rules in matching files |
| `extend-per-file-ignores` | Adds file-specific ignored rules |
| `dummy-variable-rgx` | Recognizes intentionally unused variable names for applicable rules |
| `task-tags` | Configures task markers for applicable comment rules |
| `quote-style` | Preferred string quotation style |
| `indent-style` | Space or tab indentation |
| `line-ending` | Line-ending convention |
| `skip-magic-trailing-comma` | Controls the trailing-comma formatting signal |
| `docstring-code-format` | Formats supported code examples in docstrings |
| `docstring-code-line-length` | Sets the line length for supported docstring code examples |

---

# Final Recommendation

For your Django and Python projects, start with `pyproject.toml` because you are already learning to manage dependencies with `uv`.

Remember these three sections:

```toml
# General Ruff configuration.
[tool.ruff]

# Linting configuration.
[tool.ruff.lint]

# Formatting configuration.
[tool.ruff.format]
```

The standalone `ruff.toml` uses:

```toml
# General configuration goes at the top level.

[lint]

[format]
```

To learn more about any available setting, use:

```bash
uv run ruff config
```

To inspect the active settings for a file, use:

```bash
uv run ruff check manage.py --show-settings
```

These commands help you explore Ruff's configuration systematically instead of trying to memorize every option.
