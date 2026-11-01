# Ruff: Complete Python Linter and Formatter Guide

## Table of Contents

1. What is Ruff?
2. Understanding the different installation methods
3. Method 1: Run Ruff temporarily with `uvx`
4. Method 2: Install Ruff permanently with `uv tool`
5. Method 3: Install Ruff in a project using `uv`
6. Method 4: Install Ruff using `pip`
7. Method 5: Install Ruff using `pipx`
8. Understanding the differences between installation methods
9. Ruff commands
10. Linting versus formatting
11. Automatically fixing issues
12. Configuring Ruff in `pyproject.toml`
13. Working with Django projects
14. Using Ruff with Docker
15. Updating and uninstalling Ruff
16. Troubleshooting
17. Recommended workflow
18. Quick reference

---

# 1. What Is Ruff?

Ruff is a Python code-quality tool that provides two main features:

- **Linting:** Detects selected code-quality issues and common programming mistakes.
- **Formatting:** Automatically formats Python code according to consistent style rules.

For example, Ruff can identify unused imports and help organize imports.

It can also format your Python code to use consistent indentation and spacing.

Official documentation:

https://docs.astral.sh/ruff/

## 1.1 Ruff does not execute your application

Suppose you have this code:

```python
import os

def greet(name):
    print(f"Hello, {name}")

greet("Nzegge")
```

The `os` module is imported but never used.

Ruff can detect this problem.

However, Ruff does not replace Python itself, Django tests, or a debugger.

---

# 2. Understanding Ruff Installation Methods

There are several ways to use Ruff.

| Method | Installation type | Best use |
|---|---|---|
| `uvx ruff` | Temporary, isolated tool environment | Trying Ruff without installing it permanently |
| `uv tool install ruff` | Persistent standalone tool installation | Using Ruff across multiple projects |
| `uv add --dev ruff` | Project development dependency | Managing Ruff as part of a project |
| `uv pip install ruff` | Installs into the selected Python environment | Adding Ruff to an existing environment |
| `pip install ruff` | Installs into the Python environment selected by `pip` | Traditional Python package installation |
| `pipx install ruff` | Persistent isolated tool installation | Using Ruff as a standalone application |

**Important distinction:** Temporary execution does not mean Ruff never downloads anything. `uvx` may download Ruff and cache it for reuse. You do not need to install Ruff as a permanent command-line tool.

The official `uv` documentation explains temporary execution and persistent tool installation:

https://docs.astral.sh/uv/guides/tools/

---

# 3. Method 1: Run Ruff Temporarily with uvx

## 3.1 What is uvx?

`uvx` is a convenient alias for:

```bash
uv tool run
```

It runs a Python tool in an isolated environment managed by `uv`.

You can use Ruff without adding it to your project's dependencies.

## 3.2 Check Python files

Navigate to your project directory:

```bash
cd /e/UV-PythonPackageManager
```

This path is for Git Bash on Windows. In PowerShell, use the appropriate Windows path.

Run:

```bash
uvx ruff check .
```

Meaning:

- `uvx`: Run a tool in an isolated environment.
- `ruff`: The tool to execute.
- `check`: Perform linting.
- `.`: Check the current directory and its applicable subdirectories.

Ruff is not added to your project's `pyproject.toml` by this command.

## 3.3 Format Python files

```bash
uvx ruff format .
```

This formats Python files in the current directory.

## 3.4 Check one file

```bash
uvx ruff check main.py
```

## 3.5 Format one file

```bash
uvx ruff format main.py
```

## 3.6 Use a particular Ruff version

You can request an exact version:

```bash
uvx ruff@0.17.1 check .
```

You can also request the latest available version:

```bash
uvx ruff@latest check .
```

The version number above is an example of version pinning; check the official Ruff installation documentation for current releases.

## 3.7 When should you use uvx?

Use `uvx` when:

- You want to try Ruff.
- You don't want to add Ruff to a project's dependencies.
- You want to check a simple Python script.
- You need to run Ruff occasionally.

For example:

```bash
uvx ruff check .
uvx ruff format .
```

**Remember:** `uvx` is for running tools, not for declaring your application's dependencies.

---

# 4. Method 2: Install Ruff Permanently with uv tool

If you use Ruff frequently, install it as a standalone command-line tool.

## 4.1 Install Ruff

```bash
uv tool install ruff
```

This installs Ruff into an environment managed by `uv`.

It does not add Ruff to the dependencies in your current project's `pyproject.toml`.

## 4.2 Verify the installation

```bash
ruff --version
```

Expected output resembles:

```text
ruff 0.x.x
```

The actual version depends on the release installed.

## 4.3 Use Ruff anywhere

Once the command is available on your `PATH`, you can run:

```bash
ruff check .
```

Format files:

```bash
ruff format .
```

Check a specific file:

```bash
ruff check main.py
```

Format a specific file:

```bash
ruff format main.py
```

You do not need to activate a project's virtual environment merely to use the standalone Ruff executable.

## 4.4 If the ruff command is not recognized

If you receive an error such as:

```text
ruff: command not found
```

or, in PowerShell:

```text
ruff is not recognized as the name of a cmdlet
```

Try:

```bash
uv tool update-shell
```

Then restart your terminal.

If necessary, check the `uv` tool installation directory and verify that its executable directory is on your `PATH`.

## 4.5 Update Ruff

```bash
uv tool upgrade ruff
```

## 4.6 Uninstall Ruff

```bash
uv tool uninstall ruff
```

This removes the standalone tool installation. It does not remove Ruff from any separate project where Ruff was installed as a dependency.

## 4.7 When should you use uv tool install?

Use it when:

- You regularly work on many Python projects.
- You want the `ruff` command available globally.
- You don't want to declare Ruff separately in every project.
- You want to manage Ruff independently of application dependencies.

---

# 5. Method 3: Install Ruff in a Project Using uv

This is particularly useful for Django applications and collaborative projects.

Suppose your project looks like this:

```text
django_store/
├── manage.py
├── pyproject.toml
├── uv.lock
├── config/
│   ├── settings.py
│   └── urls.py
└── products/
    ├── models.py
    ├── views.py
    └── serializers.py
```

You want Ruff to be part of this project's development tools.

## 5.1 Navigate into the project

```bash
cd django_store
```

Make sure this is the directory containing your project's `pyproject.toml`.

## 5.2 Add Ruff as a development dependency

```bash
uv add --dev ruff
```

This declares Ruff as a development dependency and updates the project's dependency lock file.

Your `pyproject.toml` will contain a Ruff dependency entry, typically in a development dependency group.

For example:

```toml
[dependency-groups]
dev = [
    "ruff",
]
```

Your existing dependencies and configuration may differ.

## 5.3 Run Ruff

Check your code:

```bash
uv run ruff check .
```

Format your code:

```bash
uv run ruff format .
```

Fix supported linting issues:

```bash
uv run ruff check . --fix
```

## 5.4 Why use uv add --dev?

Consider two projects:

```text
project_a/
├── pyproject.toml
└── uv.lock

project_b/
├── pyproject.toml
└── uv.lock
```

If Ruff is installed only as a standalone tool, neither project's `pyproject.toml` necessarily declares it.

If you add Ruff to `project_a` using:

```bash
uv add --dev ruff
```

Then Ruff is declared in `project_a`.

Someone setting up that project can install its dependencies with:

```bash
uv sync
```

The development dependency is included by default.

This is useful for teams because the project records which development tools it uses.

## 5.5 Use Ruff without activating the virtual environment

You can run:

```bash
uv run ruff check .
```

You do not need to activate `.venv` first.

`uv run` selects the project's environment and runs the command there.

## 5.6 When should you use uv add --dev ruff?

Use it when:

- Ruff belongs to a particular project.
- You want to record Ruff in `pyproject.toml`.
- You want reproducible development environments.
- You are working with Django, Flask, FastAPI, or other Python applications.
- You want other developers to use the same project's development tools.

**Recommendation:** For a project managed by `uv`, this is generally the best choice.

---

# 6. Method 4: Install Ruff Using uv pip

You can also use `uv pip` to install Ruff into a selected Python environment.

For example, create a virtual environment:

```bash
uv venv
```

On Windows PowerShell, activate it:

```powershell
.venv\Scripts\Activate.ps1
```

On Git Bash:

```bash
source .venv/Scripts/activate
```

Install Ruff into the active environment:

```bash
uv pip install ruff
```

Verify:

```bash
ruff --version
```

This method installs Ruff into the selected environment, but it does not automatically declare Ruff as a dependency in `pyproject.toml`.

That distinction matters.

If you delete `.venv`, Ruff installed only in that environment will also disappear from that environment.

For a `uv` project, prefer:

```bash
uv add --dev ruff
```

when you want the project to track Ruff as a development dependency.

---

# 7. Method 5: Install Ruff Using pip

You can install Ruff using traditional Python package-management commands.

```bash
python -m pip install ruff
```

Verify:

```bash
python -m ruff --version
```

The `python -m pip` form makes it clearer which Python interpreter is being used to run pip.

However, the `ruff` command itself may not be available if the installation's executable directory is not on your `PATH`.

For project work, install Ruff inside the project's virtual environment rather than accidentally installing it into an unrelated Python environment.

If you are managing the project with `uv`, use `uv add --dev ruff` instead when you want the dependency recorded in the project.

---

# 8. Method 6: Install Ruff Using pipx

`pipx` installs Python command-line applications in isolated environments.

Install Ruff with:

```bash
pipx install ruff
```

Verify:

```bash
ruff --version
```

Update:

```bash
pipx upgrade ruff
```

Uninstall:

```bash
pipx uninstall ruff
```

This is similar in purpose to:

```bash
uv tool install ruff
```

If you already use `uv`, you usually do not need to install `pipx` solely to manage Ruff.

---

# 9. Understanding the Installation Differences

Consider these commands:

```bash
uvx ruff check .
```

```bash
uv tool install ruff
```

```bash
uv add --dev ruff
```

They have different purposes.

| Feature | `uvx ruff` | `uv tool install ruff` | `uv add --dev ruff` |
|---|---|---|---|
| Runs Ruff | Yes | Yes | Yes, using `uv run` |
| Requires permanent standalone installation | No | Yes | No separate standalone installation required |
| Adds Ruff to project dependencies | No | No | Yes |
| Updates project `pyproject.toml` | No | No | Yes |
| Updates project `uv.lock` | No | No | Yes |
| Useful across many projects | Yes | Yes | Mainly for the declaring project |
| Recommended for occasional use | Yes | Possible | Possible |
| Recommended for a managed project | Possible | Possible | Yes |

## Important note about "permanent"

Permanent does not mean the tool can never be updated or removed.

It means the installation persists beyond a single command.

- `uvx`: Runs Ruff without requiring a persistent standalone installation.
- `uv tool install`: Maintains a standalone Ruff installation.
- `uv add --dev`: Declares Ruff as part of the project's development dependencies.

---

# 10. Ruff Commands

## 10.1 Linting commands

Check the current project:

```bash
uv run ruff check .
```

Check a single file:

```bash
uv run ruff check main.py
```

Check a directory:

```bash
uv run ruff check src/
```

Check selected Python files:

```bash
uv run ruff check main.py utils.py
```

Check and display available fixes without applying them:

```bash
uv run ruff check . --diff
```

Automatically apply supported fixes:

```bash
uv run ruff check . --fix
```

Explain a particular Ruff rule:

```bash
uv run ruff rule F401
```

`F401` is the rule for an imported name that is unused.

## 10.2 Formatting commands

Format the current project:

```bash
uv run ruff format .
```

Format one file:

```bash
uv run ruff format main.py
```

Check whether formatting changes are needed:

```bash
uv run ruff format --check .
```

Show the formatting changes without applying them:

```bash
uv run ruff format --diff .
```

## 10.3 Check Ruff's version

```bash
uv run ruff --version
```

## 10.4 View command help

```bash
uv run ruff --help
```

For linting options:

```bash
uv run ruff check --help
```

For formatting options:

```bash
uv run ruff format --help
```

---

# 11. Linting Versus Formatting

These two commands do different jobs.

## 11.1 Linting

Create `example.py`:

```python
import os
import sys


def greet(name):
    print(f"Hello, {name}")


greet("Python")
```

Here, `os` and `sys` are imported but not used.

Run:

```bash
uv run ruff check example.py
```

Ruff can report the unused imports.

You can apply supported automatic fixes:

```bash
uv run ruff check example.py --fix
```

## 11.2 Formatting

Consider this code:

```python
def greet(name):
  print(  f"Hello, {name}"  )
```

Run:

```bash
uv run ruff format example.py
```

Ruff will standardize the formatting, for example:

```python
def greet(name):
    print(f"Hello, {name}")
```

## 11.3 Can you use them separately?

Yes.

You can use Ruff only as a linter:

```bash
uv run ruff check .
```

Or only as a formatter:

```bash
uv run ruff format .
```

You can also use both.

---

# 12. Automatically Fixing Problems

Ruff supports automatic fixes for many linting issues.

Run:

```bash
uv run ruff check . --fix
```

This asks Ruff to apply supported fixes.

For example, it may remove an unused import.

However, automatic fixing has limitations:

- Not every rule has an automatic fix.
- Some fixes may require a human decision.
- Formatting is a separate operation.
- Ruff cannot guarantee that your program's logic is correct.

A useful workflow is:

```bash
uv run ruff check .
uv run ruff check . --fix
uv run ruff format .
uv run ruff check .
```

The final check helps you see whether linting issues remain.

For important projects, review the changes with Git:

```bash
git diff
```

This lets you inspect what Ruff changed.

---

# 13. Configuring Ruff in pyproject.toml

You can configure Ruff in your project's `pyproject.toml`.

For example:

```toml
[tool.ruff]
line-length = 88
target-version = "py313"

[tool.ruff.lint]
select = ["E", "F", "I", "B", "UP"]
```

## 13.1 Understanding the settings

### line-length

```toml
line-length = 88
```

Sets the preferred maximum line length for formatting and applicable lint rules.

### target-version

```toml
target-version = "py313"
```

Tells Ruff to target Python 3.13.

Change this to match the Python version you intend to support.

### select

```toml
select = ["E", "F", "I", "B", "UP"]
```

Enables selected rule families:

| Rule prefix | Purpose |
|---|---|
| `E` | Selected style checks |
| `F` | Common programming errors and unused names/imports |
| `I` | Import sorting |
| `B` | Bugbear checks for potential bugs and design problems |
| `UP` | Suggestions for modern Python syntax |

These rules may report issues that need manual review.

You can learn about the rules here:

https://docs.astral.sh/ruff/rules/

## 13.2 Run Ruff using the configuration

Once the configuration is saved, run:

```bash
uv run ruff check .
```

Or format the project:

```bash
uv run ruff format .
```

Ruff reads the applicable configuration automatically.

You generally do not need to repeat the configuration settings on every command.

---

# 14. Working with Django Projects

Suppose your Django project looks like this:

```text
django_store/
├── Dockerfile
├── manage.py
├── pyproject.toml
├── uv.lock
├── config/
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   └── wsgi.py
└── products/
    ├── models.py
    ├── views.py
    ├── serializers.py
    └── tests.py
```

## 14.1 Install Ruff as a development dependency

From the directory containing `pyproject.toml`, run:

```bash
uv add --dev ruff
```

## 14.2 Check your Django code

```bash
uv run ruff check .
```

## 14.3 Fix supported issues

```bash
uv run ruff check . --fix
```

## 14.4 Format the Django project

```bash
uv run ruff format .
```

## 14.5 Check formatting without changing files

```bash
uv run ruff format --check .
```

Ruff can check Python files such as:

- `settings.py`
- `urls.py`
- `models.py`
- `views.py`
- `serializers.py`
- `tests.py`

Ruff does not replace Django's system checks or tests.

You should still run:

```bash
uv run python manage.py check
```

And your tests:

```bash
uv run python manage.py test
```

These commands serve different purposes.

---

# 15. Using Ruff with Docker

Ruff can be used during development without being installed in your production container.

For example, if Ruff is declared in the development dependency group:

```bash
uv add --dev ruff
```

You can run Ruff locally:

```bash
uv run ruff check .
```

Your Docker build can then choose whether to install development dependencies.

For a typical production image, you may install only production dependencies:

```dockerfile
RUN uv sync --locked --no-dev
```

This excludes the development dependency group, including Ruff if it is declared there.

For development, you can use:

```dockerfile
RUN uv sync --locked
```

This includes the development dependencies by default.

**Best practice:** Keep linting and formatting tools in development dependencies unless your deployment workflow specifically needs them.

---

# 16. Updating and Uninstalling Ruff

## 16.1 Update a standalone installation

```bash
uv tool upgrade ruff
```

## 16.2 Remove a standalone installation

```bash
uv tool uninstall ruff
```

## 16.3 Update a project dependency

For a project-managed Ruff dependency, use:

```bash
uv add --dev ruff
```

This updates the declared development dependency and resolves the project environment according to the dependency specification.

If you need to update an existing Ruff dependency within the project's allowed constraints, you can use:

```bash
uv lock --upgrade-package ruff
```

Then synchronize the environment:

```bash
uv sync
```

## 16.4 Remove Ruff from a project

```bash
uv remove --dev ruff
```

This removes Ruff from the project's development dependencies and updates the project configuration and lock file.

## 16.5 Remove Ruff from a pip-managed environment

Activate the appropriate virtual environment, then run:

```bash
python -m pip uninstall ruff
```

Be careful to uninstall Ruff from the environment where it was actually installed.

---

# 17. Troubleshooting

## Problem 1: `uvx` is not recognized

Check:

```bash
uv --version
```

If `uv` works, you can invoke the full command:

```bash
uv tool run ruff check .
```

If `uv` itself is not recognized, fix the installation or `PATH` configuration for `uv`.

## Problem 2: `ruff` is not recognized

If Ruff was installed using:

```bash
uv tool install ruff
```

try:

```bash
uv tool update-shell
```

Restart your terminal if needed.

If Ruff was installed as a project dependency, use:

```bash
uv run ruff check .
```

## Problem 3: `uv run ruff` cannot find Ruff

Make sure you installed Ruff in the project:

```bash
uv add --dev ruff
```

Then:

```bash
uv run ruff check .
```

Also confirm that you are in the directory containing the intended `pyproject.toml`.

## Problem 4: Ruff reports errors after formatting

Formatting and linting are different operations.

Run:

```bash
uv run ruff check .
```

Then examine the reported rule codes and messages.

For supported automatic fixes:

```bash
uv run ruff check . --fix
```

Some issues must be fixed manually.

## Problem 5: You want to try Ruff without modifying your project

Run:

```bash
uvx ruff check .
```

This does not add Ruff to `pyproject.toml`.

If you use a configured Ruff version in your project, use that version for consistent results.

---

# 18. Recommended Workflow

For a project managed by `uv`, I recommend the following process.

## Step 1: Add Ruff to development dependencies

```bash
uv add --dev ruff
```

## Step 2: Configure Ruff

Add this to `pyproject.toml` if you want these example settings:

```toml
[tool.ruff]
line-length = 88
target-version = "py313"

[tool.ruff.lint]
select = ["E", "F", "I", "B", "UP"]
```

Choose a `target-version` appropriate for your project.

## Step 3: Check the code

```bash
uv run ruff check .
```

## Step 4: Apply supported fixes

```bash
uv run ruff check . --fix
```

## Step 5: Format the code

```bash
uv run ruff format .
```

## Step 6: Check again

```bash
uv run ruff check .
```

## Step 7: Review the changes

```bash
git diff
```

## Step 8: Run the application's checks and tests

For Django:

```bash
uv run python manage.py check
uv run python manage.py test
```

---

# 19. Quick Reference

| Goal | Command |
|---|---|
| Run Ruff temporarily | `uvx ruff check .` |
| Run the full tool command | `uv tool run ruff check .` |
| Install Ruff as a standalone tool | `uv tool install ruff` |
| Install Ruff in a project | `uv add --dev ruff` |
| Check Python code | `uv run ruff check .` |
| Check one file | `uv run ruff check main.py` |
| Fix supported linting issues | `uv run ruff check . --fix` |
| Format the project | `uv run ruff format .` |
| Format one file | `uv run ruff format main.py` |
| Check formatting only | `uv run ruff format --check .` |
| Preview linting fixes | `uv run ruff check . --diff` |
| Preview formatting changes | `uv run ruff format --diff .` |
| Show version | `uv run ruff --version` |
| Upgrade standalone Ruff | `uv tool upgrade ruff` |
| Uninstall standalone Ruff | `uv tool uninstall ruff` |
| Remove project Ruff dependency | `uv remove --dev ruff` |

---

# Final Advice

Remember these three commands:

```bash
# Temporary: try Ruff without adding it to your project
uvx ruff check .

# Standalone: keep Ruff installed for use across projects
uv tool install ruff

# Project-managed: record Ruff as a development dependency
uv add --dev ruff
```

For your Django projects managed by `uv`, **`uv add --dev ruff` is generally the best choice** because it records Ruff in the project's dependency configuration.

Use `uvx` when experimenting, and use `uv tool install` when you want a standalone Ruff command available across projects.

Official references:

- Ruff installation: https://docs.astral.sh/ruff/installation/
- Ruff linting: https://docs.astral.sh/ruff/linter/
- Ruff formatting: https://docs.astral.sh/ruff/formatter/
- uv tools: https://docs.astral.sh/uv/guides/tools/
