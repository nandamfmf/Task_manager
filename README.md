# Task Manager (CLI)

## About the Project

A command-line application built in Python to manage tasks directly from the terminal.

The application allows users to add, list, complete, remove, filter, and edit tasks.

Each task has an **ID**, **description**, **status** (pending or completed), and **creation date**.

Tasks are stored in a local JSON file (`tarefas.json`), allowing them to persist even after the program is closed.

## Technologies

- **Python 3.8 or higher**
- Python standard library:
  - `argparse` — command-line argument parsing
  - `json` — data storage
  - `datetime` — date handling
  - `pathlib` — file path management
  - `os` — operating system interaction
  - `unicodedata` — text normalization
- **pytest** — used only for running automated tests (optional for using the application)

## Installation

**Prerequisite:** Python must be installed on your system. You can download it from the [official Python website](https://www.python.org/downloads/).

To check your Python version:

```bash
python --version
```

On Linux/macOS, you may need to use `python3` instead.

### Clone and Run

```bash
git clone https://github.com/nandamfmf/Task_manager.git
cd Task_manager
python tarefas.py --help
```

No additional dependencies are required to run the application.

**Note:** On Linux/macOS, if the `python` command is not available, use `python3` instead.

## Usage

General command format:

```bash
python tarefas.py <command> [arguments]
```

Running the program without arguments displays the help message.

### Available Commands

| Command | Description |
|---|---|
| `adicionar "description"` | Creates a new pending task |
| `listar` | Lists all tasks |
| `concluir ID` | Marks a task as completed |
| `remover ID` | Removes a task |
| `filtrar pendente` / `filtrar concluida` | Displays tasks with a specific status (`concluída` with an accent is also accepted) |
| `editar ID "new description"` | Updates a task's description |
| `--help` or `<command> --help` | Displays available commands and their arguments |

Use quotation marks when a task description contains spaces.

### Usage Examples

```text
python tarefas.py adicionar "Study Git"
Task #1 added.

python tarefas.py adicionar "Read argparse documentation"
Task #2 added.

python tarefas.py listar
[ ] #1 Study Git 10/10/2026
[ ] #2 Read argparse documentation 10/10/2026

python tarefas.py concluir 1
Task #1 completed.

python tarefas.py filtrar pendente
[ ] #2 Read argparse documentation 10/10/2026

python tarefas.py filtrar concluida
[x] #1 Study Git 10/10/2026

python tarefas.py editar 2 "Read pytest documentation"
Task #2 updated: Read pytest documentation

python tarefas.py remover 1
Task #1 removed.

python tarefas.py listar
[ ] #2 Read pytest documentation 10/10/2026
```

When the task list is empty:

```text
$ python tarefas.py listar
No tasks found.
```

### Error Handling

Errors are displayed in the terminal through `stderr`, and the program exits with a non-zero status code.

Examples:

```text
$ python tarefas.py concluir 99
Error: Task with ID 99 not found.

$ python tarefas.py adicionar "   "
Error: Task description cannot be empty.

$ python tarefas.py concluir abc
usage: tarefas.py concluir [-h] id
tarefas.py concluir: error: argument id: invalid int value: 'abc'

$ python tarefas.py voar
usage: tarefas.py [-h] {adicionar,listar,concluir,remover,filtrar,editar} ...
tarefas.py: error: argument comando: invalid choice: 'voar'
```

### Data Storage

Tasks are stored in `tarefas.json`, located in the project directory (or in the executable's directory when using the packaged version).

The file is created automatically when the first task is added and is not tracked by Git, as it is included in `.gitignore`.

The application uses a fixed file path, so the data location does not change depending on the directory from which the command is executed.

### Building an Executable (Optional)

The application can be packaged into a standalone executable that runs **without requiring Python to be installed**, using [PyInstaller](https://pyinstaller.org/).

The executable must be generated on the target operating system:

- Windows executable: build on Windows
- Linux executable: build on Linux
- macOS executable: build on macOS

Install PyInstaller:

```bash
pip install pyinstaller
```

Build the executable:

```bash
pyinstaller --onefile --name tarefas tarefas.py
```

The executable will be generated in the `dist/` directory:

- Windows: `dist/tarefas.exe`
- Linux/macOS: `dist/tarefas`

### Running the Executable

**Windows (PowerShell or CMD):**

```bash
dist\tarefas.exe adicionar "Study Git"
dist\tarefas.exe listar
```

**Linux/macOS:**

```bash
./dist/tarefas adicionar "Study Git"
./dist/tarefas listar
```

The commands remain the same as those listed above. Simply replace `python tarefas.py` with the executable path.

When using the executable, `tarefas.json` is created in the same directory as the executable. Make sure it is located in a directory where you have write permissions.

The `build/` and `dist/` directories, as well as the `tarefas.spec` file, are generated by PyInstaller and are already included in `.gitignore`.

**Note:** Windows or antivirus software may flag an unsigned executable generated with PyInstaller. This is common for applications that have not been digitally signed.

### Running Tests

Install pytest:

```bash
pip install pytest
```

Run the automated tests:

```bash
python -m pytest
```

## Development Process

Although I had previously developed a task manager in another programming language, this was my first time using Python for a project involving object-oriented programming and exploring the `argparse` module.

Overall, I found Python to be a great choice for this project. Its versatility and standard library make it possible to learn and apply useful tools with each new project.

### Code Structure

```text
Task_manager/
├── tarefas.py             # Entry point: calls main()
├── src/
│   ├── main.py             # CLI: parses commands and displays messages
│   ├── tarefa.py           # Business logic: task management
│   └── armazenamento.py    # Data storage: saves and loads JSON
└── testes/                 # Automated tests using pytest
```

### Design Decisions

- **JSON for data storage:** A simple, readable format that makes data easy to inspect and debug.
- **argparse with subcommands:** Provides built-in help messages and automatically validates command-line arguments.
- **Fixed data file path:** Keeps `tarefas.json` in a consistent location, preventing the application from reading or writing different files depending on the current working directory.
- **Object-oriented design:** Organizes task-related data and operations into classes, making the code easier to maintain and extend.

### Challenges and Solutions

- **Task description validation:** I discovered that the function responsible for editing a task's description was accepting strings containing only whitespace. I implemented validation to prevent empty descriptions from being stored.
- **Accent-insensitive text handling:** I wanted the application to accept both `concluída` and `concluida` when filtering completed tasks. I implemented a `normalizar_texto` function using Python's `unicodedata` module.
- **Task ID management:** Initially, I used the list size to generate new IDs. However, removing a task from the middle of the list could cause incorrect ID assignments. I changed the implementation to generate each new ID using the highest existing ID plus one, ensuring that IDs are not reused after deletions.

## Future Improvements

- Expand the automated test suite to improve validation and reliability.
- Add more features to make task management more complete.
- Develop a graphical user interface to complement the command-line application.# Task Manager (CLI)

## About the Project

A command-line application built in Python to manage tasks directly from the terminal.

The application allows users to add, list, complete, remove, filter, and edit tasks.

Each task has an **ID**, **description**, **status** (pending or completed), and **creation date**.

Tasks are stored in a local JSON file (`tarefas.json`), allowing them to persist even after the program is closed.

## Technologies

- **Python 3.8 or higher**
- Python standard library:
  - `argparse` — command-line argument parsing
  - `json` — data storage
  - `datetime` — date handling
  - `pathlib` — file path management
  - `os` — operating system interaction
  - `unicodedata` — text normalization
- **pytest** — used only for running automated tests (optional for using the application)

## Installation

**Prerequisite:** Python must be installed on your system. You can download it from the [official Python website](https://www.python.org/downloads/).

To check your Python version:

```bash
python --version
```

On Linux/macOS, you may need to use `python3` instead.

### Clone and Run

```bash
git clone https://github.com/nandamfmf/Task_manager.git
cd Task_manager
python tarefas.py --help
```

No additional dependencies are required to run the application.

**Note:** On Linux/macOS, if the `python` command is not available, use `python3` instead.

## Usage

General command format:

```bash
python tarefas.py <command> [arguments]
```

Running the program without arguments displays the help message.

### Available Commands

| Command | Description |
|---|---|
| `adicionar "description"` | Creates a new pending task |
| `listar` | Lists all tasks |
| `concluir ID` | Marks a task as completed |
| `remover ID` | Removes a task |
| `filtrar pendente` / `filtrar concluida` | Displays tasks with a specific status (`concluída` with an accent is also accepted) |
| `editar ID "new description"` | Updates a task's description |
| `--help` or `<command> --help` | Displays available commands and their arguments |

Use quotation marks when a task description contains spaces.

### Usage Examples

```text
python tarefas.py adicionar "Study Git"
Task #1 added.

python tarefas.py adicionar "Read argparse documentation"
Task #2 added.

python tarefas.py listar
[ ] #1 Study Git 10/10/2026
[ ] #2 Read argparse documentation 10/10/2026

python tarefas.py concluir 1
Task #1 completed.

python tarefas.py filtrar pendente
[ ] #2 Read argparse documentation 10/10/2026

python tarefas.py filtrar concluida
[x] #1 Study Git 10/10/2026

python tarefas.py editar 2 "Read pytest documentation"
Task #2 updated: Read pytest documentation

python tarefas.py remover 1
Task #1 removed.

python tarefas.py listar
[ ] #2 Read pytest documentation 10/10/2026
```

When the task list is empty:

```text
$ python tarefas.py listar
No tasks found.
```

### Error Handling

Errors are displayed in the terminal through `stderr`, and the program exits with a non-zero status code.

Examples:

```text
$ python tarefas.py concluir 99
Error: Task with ID 99 not found.

$ python tarefas.py adicionar "   "
Error: Task description cannot be empty.

$ python tarefas.py concluir abc
usage: tarefas.py concluir [-h] id
tarefas.py concluir: error: argument id: invalid int value: 'abc'

$ python tarefas.py voar
usage: tarefas.py [-h] {adicionar,listar,concluir,remover,filtrar,editar} ...
tarefas.py: error: argument comando: invalid choice: 'voar'
```

### Data Storage

Tasks are stored in `tarefas.json`, located in the project directory (or in the executable's directory when using the packaged version).

The file is created automatically when the first task is added and is not tracked by Git, as it is included in `.gitignore`.

The application uses a fixed file path, so the data location does not change depending on the directory from which the command is executed.

### Building an Executable (Optional)

The application can be packaged into a standalone executable that runs **without requiring Python to be installed**, using [PyInstaller](https://pyinstaller.org/).

The executable must be generated on the target operating system:

- Windows executable: build on Windows
- Linux executable: build on Linux
- macOS executable: build on macOS

Install PyInstaller:

```bash
pip install pyinstaller
```

Build the executable:

```bash
pyinstaller --onefile --name tarefas tarefas.py
```

The executable will be generated in the `dist/` directory:

- Windows: `dist/tarefas.exe`
- Linux/macOS: `dist/tarefas`

### Running the Executable

**Windows (PowerShell or CMD):**

```bash
dist\tarefas.exe adicionar "Study Git"
dist\tarefas.exe listar
```

**Linux/macOS:**

```bash
./dist/tarefas adicionar "Study Git"
./dist/tarefas listar
```

The commands remain the same as those listed above. Simply replace `python tarefas.py` with the executable path.

When using the executable, `tarefas.json` is created in the same directory as the executable. Make sure it is located in a directory where you have write permissions.

The `build/` and `dist/` directories, as well as the `tarefas.spec` file, are generated by PyInstaller and are already included in `.gitignore`.

**Note:** Windows or antivirus software may flag an unsigned executable generated with PyInstaller. This is common for applications that have not been digitally signed.

### Running Tests

Install pytest:

```bash
pip install pytest
```

Run the automated tests:

```bash
python -m pytest
```

## Development Process

Although I had previously developed a task manager in another programming language, this was my first time using Python for a project involving object-oriented programming and exploring the `argparse` module.

Overall, I found Python to be a great choice for this project. Its versatility and standard library make it possible to learn and apply useful tools with each new project.

### Code Structure

```text
Task_manager/
├── tarefas.py             # Entry point: calls main()
├── src/
│   ├── main.py             # CLI: parses commands and displays messages
│   ├── tarefa.py           # Business logic: task management
│   └── armazenamento.py    # Data storage: saves and loads JSON
└── testes/                 # Automated tests using pytest
```

### Design Decisions

- **JSON for data storage:** A simple, readable format that makes data easy to inspect and debug.
- **argparse with subcommands:** Provides built-in help messages and automatically validates command-line arguments.
- **Fixed data file path:** Keeps `tarefas.json` in a consistent location, preventing the application from reading or writing different files depending on the current working directory.
- **Object-oriented design:** Organizes task-related data and operations into classes, making the code easier to maintain and extend.

### Challenges and Solutions

- **Task description validation:** I discovered that the function responsible for editing a task's description was accepting strings containing only whitespace. I implemented validation to prevent empty descriptions from being stored.
- **Accent-insensitive text handling:** I wanted the application to accept both `concluída` and `concluida` when filtering completed tasks. I implemented a `normalizar_texto` function using Python's `unicodedata` module.
- **Task ID management:** Initially, I used the list size to generate new IDs. However, removing a task from the middle of the list could cause incorrect ID assignments. I changed the implementation to generate each new ID using the highest existing ID plus one, ensuring that IDs are not reused after deletions.

## Future Improvements

- Expand the automated test suite to improve validation and reliability.
- Add more features to make task management more complete.
- Develop a graphical user interface to complement the command-line application.
