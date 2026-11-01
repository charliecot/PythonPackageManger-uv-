# `uv` Command Reference: Beginner to Advanced

A practical reference for learning `uv`, the Python package, project, environment, and Python-version manager.

> **Important:** `uv` does not replace the Python interpreter. It helps install/manage Python versions, create environments, manage dependencies, and run Python programs.
>
> **Version note:** Commands and flags can change between `uv` releases. Use `uv --help` or the command-specific `--help` option to verify what your installed version supports.

---

## Table of contents

1. [Core concepts](#1-core-concepts)
2. [Installation and help](#2-installation-and-help)
3. [Python version commands](#3-python-version-commands)
4. [Creating and running projects](#4-creating-and-running-projects)
5. [Managing project dependencies](#5-managing-project-dependencies)
6. [Virtual environments](#6-virtual-environments)
7. [The `uv pip` interface](#7-the-uv-pip-interface)
8. [Locking, syncing, and exporting](#8-locking-syncing-and-exporting)
9. [Running scripts](#9-running-scripts)
10. [Command-line tools with `uv tool` and `uvx`](#10-command-line-tools-with-uv-tool-and-uvx)
11. [Build and publish commands](#11-build-and-publish-commands)
12. [Caches and performance](#12-caches-and-performance)
13. [Configuration and environment variables](#13-configuration-and-environment-variables)
14. [Common workflows](#14-common-workflows)
15. [Troubleshooting](#15-troubleshooting)
16. [Best practices](#16-best-practices)
17. [Quick command cheat sheet](#17-quick-command-cheat-sheet)

---

## 1. Core concepts

| Term | Meaning |
|---|---|
| `uv` | A fast tool for Python package management, project management, and Python-version management. |
| Python interpreter | The program that executes Python code. |
| Package | Reusable Python code that can be installed, such as `requests` or `Django`. |
| Dependency | A package your project needs. |
| Virtual environment | An isolated Python environment with its own installed packages. |
| `pyproject.toml` | Project configuration and declared dependencies. |
| `uv.lock` | A lockfile recording the resolved dependency versions and related information. |
| `.python-version` | A file indicating the project's preferred Python version. |
| `.venv/` | The conventional project virtual-environment directory. |
| `uv run` | Runs a command in the project environment, synchronizing it when needed. |
| `uv add` | Adds a dependency to a `uv`-managed project. |
| `uv pip install` | Installs packages using a pip-compatible interface into an environment. |
| `uvx` | A shortcut for `uv tool run`, used to run command-line tools in isolated environments. |

### Two main ways to use `uv`

**Project-managed workflow** — recommended for new applications:

```powershell
uv init my_project
cd my_project
uv add requests
uv run main.py
```

This workflow uses project configuration and a lockfile.

**Environment/package workflow** — useful when working with existing projects or requirements files:

```powershell
uv venv
uv pip install requests
```

These approaches overlap, but they are not identical. For a project managed by `uv`, prefer `uv add` for dependencies that belong in `pyproject.toml`.

---

## 2. Installation and help

### Install on Windows

In PowerShell, the official installer command is:

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

This downloads and runs the installer. Run installation scripts only from sources you trust.

Official installation instructions: <https://docs.astral.sh/uv/getting-started/installation/>

Alternative, if you already have Python and pip:

```powershell
py -m pip install uv
```

### General commands

```powershell
uv --version
# Shows the installed uv version.

uv --help
# Shows the top-level help and available command groups.

uv help
# Requests help through the uv help command, if supported by the installed version.
```

### Ask for help on a specific command

```powershell
uv python --help
# Shows Python-version management commands and options.

uv add --help
# Shows options for adding project dependencies.

uv run --help
# Shows options for running commands.

uv pip --help
# Shows the pip-compatible command group.

uv tool --help
# Shows commands for managing command-line tools.
```

**Meaning of `--help`:** Ask a command to explain its syntax, options, and usage. When unsure about an option, check help instead of guessing.

---

## 3. Python version commands

`uv` can download and manage Python installations. It can also discover interpreters already installed on your computer.

### List Python versions

```powershell
uv python list
# Lists available Python versions and detected installations.

uv python list --only-installed
# Lists Python installations already available locally.
```

### Install Python

```powershell
uv python install 3.12
# Installs a managed Python 3.12 version.

uv python install 3.12 3.13
# Requests installations for Python 3.12 and 3.13.
```

Use versions supported by your project and its dependencies.

### Find an interpreter

```powershell
uv python find
# Finds a Python interpreter using uv's normal discovery rules.

uv python find 3.12
# Finds an interpreter matching Python 3.12.
```

### Pin a project version

```powershell
uv python pin 3.12
# Creates or updates .python-version for the project.
```

A `.python-version` file may contain:

```text
3.12
```

**Meaning:** This expresses the preferred Python version for the project. It does not necessarily force every tool to use that version.

### Upgrade or uninstall managed Python versions

```powershell
uv python upgrade 3.12
# Upgrades a managed Python installation in the 3.12 series, where supported.

uv python uninstall 3.12
# Uninstalls a uv-managed Python installation matching the request.
```

Be careful before uninstalling a version that a project uses. These commands manage uv-installed Python versions; they do not necessarily remove a separate Python installation installed by another method.

### Run using a requested Python version

```powershell
uv run --python 3.12 main.py
# Runs main.py using a compatible Python 3.12 interpreter.
```

For a project, keep `requires-python` in `pyproject.toml` compatible with the interpreter you select.

---

## 4. Creating and running projects

### Initialize a project

```powershell
uv init my_project
# Creates a new project directory and starter files.

cd my_project
# Changes the current directory to the new project.
```

To initialize the current directory:

```powershell
uv init
# Initializes the current directory; check `uv init --help` for options.
```

Common generated files can include:

```text
my_project/
├── .gitignore
├── .python-version
├── pyproject.toml
├── README.md
└── main.py
```

Exact files vary by project type and uv version.

### Inspect project configuration

```powershell
Get-Content pyproject.toml
# PowerShell: displays the project configuration.

Get-Content .python-version
# PowerShell: displays the preferred Python version, if the file exists.
```

### Run a Python file

```powershell
uv run main.py
# Runs main.py inside the project environment, syncing when needed.

uv run python --version
# Prints the Python version used for this command.

uv run python
# Starts Python interactively in the selected project environment.
```

### Run a module

```powershell
uv run python -m http.server 8000
# Runs Python's built-in HTTP server module on port 8000.
# Only use a server in a directory whose contents you are willing to expose.
```

### Run tests

```powershell
uv run pytest
# Runs pytest if it is declared/available in the project's environment.
```

If pytest is not a dependency, add it first:

```powershell
uv add --dev pytest
# Adds pytest as a development dependency.
```

### Initialize a library instead of an application

```powershell
uv init --lib my_library
# Initializes a library-style project, if supported by your uv version.
```

Check `uv init --help` for available project templates and flags.

---

## 5. Managing project dependencies

Project dependencies are packages your project needs. In a managed project, use `uv add` and `uv remove` to update declarations.

### Add one package

```powershell
uv add requests
# Adds requests to project dependencies and synchronizes the environment.
```

### Add several packages

```powershell
uv add requests beautifulsoup4 watchdog
# Adds all listed packages to the project.
```

### Add a development dependency

```powershell
uv add --dev pytest
# Adds pytest for development/testing rather than runtime use.
```

Depending on project configuration and uv version, development dependency groups may be represented under `[dependency-groups]` in `pyproject.toml`.

### Remove a dependency

```powershell
uv remove requests
# Removes requests from the project's declared dependencies.
```

### Add a particular version range

```powershell
uv add "requests>=2.31,<3"
# Requests a compatible requests release in the specified range.
```

The quotes protect characters such as `<` from being interpreted by the shell.

### Add an optional extra

```powershell
uv add "httpx[http2]"
# Adds httpx with its optional HTTP/2 dependencies enabled.
```

Only use extras documented by the package.

### Update a dependency

```powershell
uv lock --upgrade-package requests
# Requests a lockfile update for requests and its permitted dependencies.
```

Review the changes and run tests afterward. Use `uv lock --help` for exact upgrade options.

### Show dependencies

```powershell
uv tree
# Displays the project's dependency tree.

uv tree --help
# Shows tree output and filtering options supported by your version.
```

### Important distinction

- `uv add package`: declare a project dependency.
- `uv remove package`: remove a declared project dependency.
- `uv pip install package`: install into an environment without automatically making it a project dependency.
- `uv pip uninstall package`: uninstall from an environment.

---

## 6. Virtual environments

A virtual environment isolates packages for a project.

### Create an environment

```powershell
uv venv
# Creates a .venv directory in the current directory.

uv venv myenv
# Creates a virtual environment named myenv.

uv venv --python 3.12
# Creates .venv using Python 3.12 when available.
```

### Activate on Windows

PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
# Activates .venv in the current PowerShell session.
```

Command Prompt:

```cmd
.venv\Scripts\activate.bat
```

Git Bash:

```bash
source .venv/Scripts/activate
```

Activation changes which environment's Python and scripts are found first in the shell.

### Deactivate

```powershell
deactivate
# Leaves the currently activated virtual environment.
```

### Do I need to activate it?

No. You can often use `uv run` for a managed project:

```powershell
uv run main.py
```

### Check which interpreter is running

```powershell
uv run python -c "import sys; print(sys.executable)"
# Prints the exact Python executable used by the command.
```

### Important environment note

By default, `uv pip` looks for an appropriate environment using its discovery rules. If you need to target a particular environment, use the relevant `--python` option supported by the command, for example:

```powershell
uv pip install --python .venv\Scripts\python.exe requests
# Installs requests into the environment identified by that interpreter.
```

Check `uv pip install --help` if your installed version uses different options.

---

## 7. The `uv pip` interface

`uv pip` provides commands familiar to users of pip. It is useful for existing projects and requirements files.

### Install packages

```powershell
uv pip install requests
# Installs requests into the selected environment.

uv pip install requests watchdog beautifulsoup4
# Installs multiple packages.

uv pip install "django>=5,<6"
# Installs a Django version within the stated range.
```

### Install from a requirements file

```powershell
uv pip install -r requirements.txt
# Installs dependencies listed in requirements.txt.
```

### Uninstall

```powershell
uv pip uninstall requests
# Removes requests from the selected environment.
```

### List packages

```powershell
uv pip list
# Lists installed packages.

uv pip show requests
# Displays metadata about requests.
```

### Freeze installed packages

```powershell
uv pip freeze
# Prints installed packages in requirements-file format.
```

To save that output in PowerShell:

```powershell
uv pip freeze > requirements.txt
# Writes the installed package versions to requirements.txt.
```

This captures installed packages; it does not automatically tell you which packages are direct dependencies and which are transitive dependencies.

### Check dependencies

```powershell
uv pip check
# Checks for incompatible or missing dependencies in the environment.
```

### Install a project in editable mode

```powershell
uv pip install -e .
# Installs the current local project in editable mode, when it is a compatible package project.
```

Editable installation is often used while developing a Python package so changes to the source code are reflected without reinstalling the package each time.

---

## 8. Locking, syncing, and exporting

### `uv.lock`

A lockfile records a resolved set of package versions and related dependency information. Commit it for applications when reproducible environments are important.

### Create/update the lockfile

```powershell
uv lock
# Resolves project dependencies and creates/updates uv.lock.
```

### Synchronize the environment

```powershell
uv sync
# Makes the project's environment match its declared dependencies and lockfile.

uv sync --locked
# Requires the lockfile to be up to date rather than silently updating it.

uv sync --frozen
# Uses the existing lockfile without updating it; check your version's help for exact behavior.
```

Use `uv sync --help` to confirm flags available in your release.

**Caution:** Synchronization may remove packages that are not part of the project's intended environment. Do not use it casually on an environment that contains unrelated packages.

### Upgrade the lockfile

```powershell
uv lock --upgrade
# Allows dependencies to move to newer versions permitted by project requirements.

uv lock --upgrade-package requests
# Targets a particular package for upgrading, where supported.
```

Run tests after upgrading.

### Export requirements

```powershell
uv export --format requirements.txt --output-file requirements.txt
# Exports project dependencies to requirements.txt, if supported by your version.
```

Inspect the available export options:

```powershell
uv export --help
```

### Which file does what?

| File | Meaning |
|---|---|
| `pyproject.toml` | Project metadata and declared dependencies. |
| `uv.lock` | Resolved dependency versions and associated resolution information. |
| `requirements.txt` | A pip-compatible list of dependencies, often used for compatibility with other workflows. |
| `.python-version` | Preferred Python version. |
| `.venv/` | Local virtual environment; normally not committed to Git. |

---

## 9. Running scripts

You can use `uv` to run small scripts without creating a full project.

### Run a script

```powershell
uv run script.py
# Runs a script, using project context when applicable.
```

### Declare inline script dependencies

Example `check_site.py`:

```python
# /// script
# dependencies = [
#   "requests",
# ]
# ///

import requests

response = requests.get("https://example.com", timeout=10)
print(response.status_code)
```

Run it:

```powershell
uv run check_site.py
# Reads the script's inline dependency metadata and runs it in an appropriate environment.
```

You can also add inline metadata with:

```powershell
uv add --script check_site.py requests
# Adds requests to the script's dependency metadata, where supported.
```

Use timeouts and handle exceptions in real network programs.

Official guide: <https://docs.astral.sh/uv/guides/scripts/>

---

## 10. Command-line tools with `uv tool` and `uvx`

Some Python packages provide command-line programs rather than libraries your application imports.

### Run a tool temporarily

```powershell
uvx ruff check .
# Runs Ruff in an isolated tool environment.
```

`uvx` is an alias for `uv tool run`.

Equivalent form:

```powershell
uv tool run ruff check .
# Runs Ruff without requiring it to be a project dependency.
```

### Install a tool persistently

```powershell
uv tool install ruff
# Installs Ruff as a command-line tool for repeated use.
```

Then:

```powershell
ruff --version
# Displays the installed Ruff version if its executable is on PATH.
```

### List and remove tools

```powershell
uv tool list
# Lists tools installed by uv.

uv tool uninstall ruff
# Uninstalls Ruff from uv's tool environment.
```

### When should I use which command?

| Command | Use |
|---|---|
| `uv add ruff` | The project itself declares Ruff as a dependency. |
| `uv add --dev ruff` | Ruff is a development dependency for this project. |
| `uvx ruff check .` | Run Ruff without a persistent project installation. |
| `uv tool install ruff` | Install Ruff as a reusable command-line tool. |

---

## 11. Build and publish commands

These commands are mainly for people creating Python packages for other people to install.

### Build a package

```powershell
uv build
# Builds source and wheel distributions for a configured package project.
```

The project must be configured as a buildable package. A simple application directory may need additional configuration first.

### Publish a package

```powershell
uv publish
# Publishes built distributions to a package index when properly configured.
```

Publishing is a consequential action. Before running it, check the destination, credentials, package name, and version. Never put API tokens or passwords directly into source code or committed files.

See the official publishing/build documentation and command help for current options:
<https://docs.astral.sh/uv/>

---

## 12. Caches and performance

`uv` uses caches to avoid repeatedly downloading and building the same packages.

### Inspect cache location

```powershell
uv cache dir
# Prints the cache directory used by uv.
```

### Clear cache

```powershell
uv cache clean
# Removes cached data.
```

Cleaning the cache can free disk space, but future commands may need to download or rebuild packages again.

Use `uv cache --help` for cache commands supported by your installed version.

---

## 13. Configuration and environment variables

`uv` supports configuration settings and environment variables. The exact options depend on the uv version and command.

### Show command-specific options

```powershell
uv --help
uv sync --help
uv pip install --help
uv python install --help
```

### Common configuration concepts

- **Index:** Where packages are downloaded from.
- **Cache:** Where downloaded or built artifacts are stored.
- **Proxy:** A network intermediary used when configured for your environment.
- **Offline mode:** Restricts network access for supported operations; required data must already be available locally.
- **Constraints:** Limits acceptable versions without necessarily declaring a package as a direct dependency.
- **Overrides:** Forces a version choice and should be used carefully because it can bypass normal compatibility expectations.

Do not copy configuration flags from an unrelated version or operating system without checking help. For index configuration, credentials, proxy, and offline options, consult the official settings reference:
<https://docs.astral.sh/uv/reference/settings/>

---

## 14. Common workflows

### Workflow A: New Python application

```powershell
uv init file_monitor
cd file_monitor
uv add watchdog
uv run main.py
```

Meaning:
1. Create a project.
2. Enter its directory.
3. Add the required package.
4. Run the application.

### Workflow B: Existing project with `requirements.txt`

```powershell
cd path\to\existing_project
uv venv
uv pip install -r requirements.txt
uv run python main.py
```

Make sure the command targets the environment you intend to use. Existing projects may have special Python-version or native-library requirements.

### Workflow C: Add testing tools

```powershell
uv add --dev pytest
uv run pytest
```

### Workflow D: Recreate a cloned project

```powershell
cd path\to\cloned_project
uv sync --locked
uv run main.py
```

Use this when the repository contains a compatible `pyproject.toml` and up-to-date `uv.lock`.

### Workflow E: Check which Python is used

```powershell
uv run python --version
uv run python -c "import sys; print(sys.executable)"
```

### Workflow F: Run a one-off CLI tool

```powershell
uvx ruff check .
```

---

## 15. Troubleshooting

### Error: `uv` is not recognized

Possible causes:
- Installation did not finish.
- The executable directory is not on `PATH`.
- The terminal has not been reopened.

Try:

```powershell
uv --version
```

Reopen PowerShell and follow the official installation instructions if necessary.

### Error: `ModuleNotFoundError`

Example:

```text
ModuleNotFoundError: No module named 'requests'
```

For a project-managed workflow:

```powershell
uv add requests
uv run main.py
```

For an environment-managed workflow:

```powershell
uv venv
uv pip install requests
uv run python main.py
```

Check that installation and execution use the same environment.

### The wrong Python version is being used

```powershell
uv python list
uv run python --version
uv run python -c "import sys; print(sys.executable)"
```

Check `.python-version` and `requires-python` in `pyproject.toml`.

### Dependency resolution fails

```powershell
uv tree
Get-Content pyproject.toml
```

Inspect the conflicting requirements. Do not randomly upgrade or downgrade every package.

### Need more details about a command

```powershell
uv COMMAND --help
```

Replace `COMMAND` with the actual command, such as `sync`, `add`, `python`, or `pip install`.

---

## 16. Best practices

1. **Use one environment per project.** This helps prevent dependency conflicts.
2. **Prefer `uv init`, `uv add`, and `uv run` for new projects.** They work together as a project workflow.
3. **Commit `pyproject.toml` and `uv.lock` for applications.** This makes dependency resolution easier to reproduce.
4. **Do not commit `.venv/`.** Add it to `.gitignore`.
5. **Use `uv add --dev` for development-only dependencies** such as test tools and linters, when appropriate.
6. **Keep Python requirements realistic.** Verify that the project's dependencies support the selected interpreter.
7. **Test after dependency upgrades.** A successful installation does not prove your program still behaves correctly.
8. **Avoid mixing workflows unnecessarily.** Use `uv add` for declared project dependencies and `uv pip` when you intentionally need the pip-compatible environment workflow.
9. **Protect secrets.** Do not commit passwords, private keys, or package-index tokens.
10. **Use trusted packages and indexes.** Verify package names before installing.
11. **Read the help output.** Options can change between releases.
12. **Do not migrate a working project without a backup.** Experiment with `uv` in a separate environment first.

---

## 17. Quick command cheat sheet

| Command | Meaning |
|---|---|
| `uv --version` | Show uv version. |
| `uv --help` | Show general help. |
| `uv init my_project` | Create a project. |
| `uv add requests` | Add a project dependency. |
| `uv add --dev pytest` | Add a development dependency. |
| `uv remove requests` | Remove a project dependency. |
| `uv run main.py` | Run a script in project context. |
| `uv run python --version` | Show the Python version used. |
| `uv sync` | Synchronize project environment. |
| `uv sync --locked` | Synchronize without updating an out-of-date lockfile. |
| `uv lock` | Resolve/update the lockfile. |
| `uv tree` | Display dependency tree. |
| `uv export --help` | Show dependency export options. |
| `uv python list` | List Python versions. |
| `uv python install 3.12` | Install Python 3.12. |
| `uv python find 3.12` | Find a matching interpreter. |
| `uv python pin 3.12` | Set the project's preferred Python version. |
| `uv venv` | Create `.venv`. |
| `uv pip install requests` | Install a package into an environment. |
| `uv pip install -r requirements.txt` | Install from requirements file. |
| `uv pip uninstall requests` | Uninstall a package. |
| `uv pip list` | List installed packages. |
| `uv pip show requests` | Show package metadata. |
| `uv pip freeze` | Print installed package versions. |
| `uv pip check` | Check dependency consistency. |
| `uvx ruff check .` | Run Ruff in an isolated tool environment. |
| `uv tool install ruff` | Install Ruff as a command-line tool. |
| `uv tool list` | List installed uv tools. |
| `uv tool uninstall ruff` | Uninstall a uv-managed tool. |
| `uv build` | Build package distributions. |
| `uv publish` | Publish distributions; check credentials and destination first. |
| `uv cache dir` | Show cache directory. |
| `uv cache clean` | Clear cached data. |

---

## Official references

- Installation: <https://docs.astral.sh/uv/getting-started/installation/>
- Projects: <https://docs.astral.sh/uv/guides/projects/>
- Python versions: <https://docs.astral.sh/uv/guides/install-python/>
- Scripts: <https://docs.astral.sh/uv/guides/scripts/>
- CLI reference: <https://docs.astral.sh/uv/reference/cli/>
- Settings: <https://docs.astral.sh/uv/reference/settings/>

**Learning advice:** Practise the commands in small projects first. Understand what each command changes—project metadata, the lockfile, the virtual environment, the Python installation, or the tool installation—before using it on an important project.
