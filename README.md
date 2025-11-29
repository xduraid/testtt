# XD-Shell

## Table of Contents

- 2.[💻 Interactive Shell Mode](#interactive-shell-mode)
    - 2.1. [⏱️ Prompt Format](#prompt-format)
    - 2.2. [✏️ Readline Features](#readline-features)
        - 2.2.1. [Line Editing and Cursor Movement](#line-editing-and-cursor-movement)
        - 2.2.2. [History Navigation](#history-navigation)
        - 2.2.3. [History Search](#history-search)
        - 2.2.4. [Tab Handling](#tab-handling)
    - 2.3. [📜 Command History](#command-history)
        - 2.3.1. [In-Memory History](#in-memory-history)
        - 2.3.2. [Persistent History File](#persistent-history-file)
        - 2.3.3. [The `history` Builtin](#the-history-builtin)
    - 2.4. [🔁 Tab Completion](#tab-completion)
        - 2.4.1. [Completion Types](#completion-types)
        - 2.4.2. [Completion Behavior](#completion-behavior)

---

## 2. Interactive Shell Mode <a name="interactive-shell-mode"></a>

`xd-shell` provides an interactive environment when both `stdin` and `stdout` are connected to a terminal.  
Interactive mode enables a dynamic prompt, line editing, command history, tab completion, and job control.

---

### 2.1. Prompt Format <a name="prompt-format"></a>

The prompt displays the current user, host, and current working directory (`cwd`) as follows:

```text
user@host:cwd$
````

* The prompt ends with `$` for normal users and with `#` for root.
* The prompt is colorized for readability.
* In the `cwd` part, the `$HOME` prefix is collapsed to `~` when the current directory is inside the user's `$HOME`.
* The prompt is recomputed before each input line so it always reflects directory changes.

---

### 2.2 Readline Features <a name="readline-features"></a>

`xd-shell` uses [xd-readline](https://github.com/xduraid/xd-readline), a custom line-editing library implemented specifically for this project.
It provides interactive features such as line editing, command history, and tab completion.

---

#### 2.2.1 Line Editing and Cursor Movement <a name="line-editing-and-cursor-movement"></a>

`xd-readline` supports full editing of the input line at any cursor position.
You can move the cursor by character or word, jump to the beginning or end of the line, and delete characters or words efficiently.

| Key Combination         | Action                                               |
| ----------------------- | ---------------------------------------------------- |
| `←` / `Ctrl+B`          | Move cursor one character to the left                |
| `→` / `Ctrl+F`          | Move cursor one character to the right               |
| `Ctrl+←` / `Alt+B`      | Move cursor one word to the left                     |
| `Ctrl+→` / `Alt+F`      | Move cursor one word to the right                    |
| `Home` / `Ctrl+A`       | Move cursor to the beginning of the line             |
| `End` / `Ctrl+E`        | Move cursor to the end of the line                   |
| `Backspace` / `Ctrl+H`  | Delete character before the cursor                   |
| `Delete`                | Delete character at the cursor                       |
| `Ctrl+D`                | Delete character at the cursor, or send EOF if empty |
| `Alt+Backspace`         | Delete word before the cursor                        |
| `Alt+D` / `Ctrl+Delete` | Delete word after the cursor                         |
| `Ctrl+U`                | Delete everything before the cursor                  |
| `Ctrl+K`                | Delete everything from the cursor to the end         |
| `Ctrl+L`                | Clear the screen                                     |
| `Enter` / `Ctrl+J`      | Submit the current input line                        |

---

#### 2.2.2 History Navigation <a name="history-navigation"></a>

`xd-readline` supports navigating through history using keyboard shortcuts.

| Key Combination             | Action                                       |
| --------------------------- | -------------------------------------------- |
| `↑` / `Page Up`             | Move to the previous history entry           |
| `↓` / `Page Down`           | Move to the next history entry               |
| `Ctrl+↑` / `Ctrl+Page Up`   | Jump to the first (oldest) history entry     |
| `Ctrl+↓` / `Ctrl+Page Down` | Jump to the last (most recent) history entry |

---

#### 2.2.3 History Search <a name="history-search"></a>

`xd-readline` provides real-time, incremental search through history.
You can search backward or forward and the matching entry gets updated as you type.
The matching entry is highlighted and can be accepted, skipped, or canceled.

| Key Combination | Action                                                |
| --------------- | ----------------------------------------------------- |
| `Ctrl+R`        | Start reverse search or jump to the previous match    |
| `Ctrl+S`        | Start forward search or jump to the next match        |
| `Ctrl+G`        | Cancel the search and restore the original input line |
| `Esc Esc`       | Accept the current match and exit search mode         |

---

#### 2.2.4 Tab Handling <a name="tab-handling"></a>

`xd-readline` provides the low-level mechanics required for tab completion within `xd-shell`.
At this level, the editor does not know *what* is being completed — it only handles the user interaction around the `Tab` key.

The basic tab-handling capabilities include:

* Detecting when the user presses `Tab`.
* Detecting **double-Tab** (two consecutive `Tab` presses).
* Requesting completion candidates from `xd-shell`’s completion engine.
* Automatically inserting the chosen completion into the input buffer.
* Displaying a list of matches when multiple candidates are available.
* Cooperating smoothly with cursor movement, editing, and history.

> ℹ️ **Note:**
> The actual completion logic (files, executables, variables, aliases, etc.) is implemented entirely in the `xd-shell` project and documented in the Tab Completion section below.
> `xd-readline` only provides the editor-level behavior.

---

## 2.3 Command History <a name="command-history"></a>

`xd-shell` maintains both in-memory and persistent command history.
History is automatically loaded at startup, used during the session, and saved when the shell exits.

---

### 2.3.1 In-Memory History <a name="in-memory-history"></a>

During an interactive session, every executed command is appended to an internal history list.

* Consecutive identical commands are not duplicated.
* Commands beginning with leading whitespace are **not** added to the history list.
* The history list is capped at **`XD_RL_HISTORY_MAX` entries (default: 1000)**.
  When the limit is reached, the oldest entries are discarded automatically.
* Recalled entries can be edited or re-executed.
* In-memory history is directly used by navigation and incremental search.

---

### 2.3.2 Persistent History File <a name="persistent-history-file"></a>

When the shell starts, it loads history from the file specified by `$HISTFILE`.
If `$HISTFILE` is unset, `xd-shell` automatically sets it to `$HOME/.xdsh_history`.
The file name `.xdsh_history` comes from the macro `XD_SH_DEF_HISTFILE_NAME`.
On exit, `xd-shell` writes the new in-memory history entries to the file referenced by `$HISTFILE` (unless it has been unset during the session).

---

### 2.3.3 The `history` Builtin <a name="the-history-builtin"></a>

The `history` builtin allows viewing and manipulating the shell’s command history.
It supports listing entries, clearing the in-memory list, and reading or writing the history file.

#### Usage

```text
history [-c]
history -arw [filename]
```

#### Options

| Command / Option    | Description                                                                                         |
| ------------------- | --------------------------------------------------------------------------------------------------- |
| `history`           | Display the current history list with line numbers.                                                 |
| `history -c`        | Clear the in-memory history list (does not modify the history file).                                |
| `history -a [file]` | Append new history lines from this session to *file*. If omitted, `$HISTFILE` is used.              |
| `history -r [file]` | Read history from *file* and append it to the in-memory list. If omitted, `$HISTFILE` is used.      |
| `history -w [file]` | Write the entire in-memory history list to *file*, overwriting it. If omitted, `$HISTFILE` is used. |

---

#### Exit Status

| Condition               | Exit Code |
| ----------------------- | --------- |
| Success                 | `0`       |
| Invalid option or error | Non-zero  |

---

## 2.4 Tab Completion <a name="tab-completion"></a>

`xd-shell` provides context-aware tab completion for commands, paths, variables, and other shell entities.
The shell inspects the token at the cursor and generates a list of matching candidates.

---

### 2.4.1 Completion Types <a name="completion-types"></a>

| Type                    | Description                                                    |
| ----------------------- | -------------------------------------------------------------- |
| **Executables**         | Commands found in directories listed in `$PATH`.               |
| **Builtins**            | Shell builtins such as `cd`, `alias`, `history`, etc.          |
| **Aliases**             | Names defined via the `alias` builtin.                         |
| **Variables**           | Shell and environment variables (`$PATH`, `$HOME`, etc.).      |
| **Files & Directories** | Pathnames relative to the current directory or a typed prefix. |
| **`~` / `~user`**       | Home directory and other user home expansions.                 |

Directories are completed with a trailing `/` when appropriate.

---

### 2.4.2 Completion Behavior <a name="completion-behavior"></a>

* A completion request inserts the only matching candidate, or the longest common prefix if multiple exist.
* Repeating the request displays all matching candidates.
* Completion adapts to context:

  * At the start of a line → commands, builtins, aliases
  * After a command → filenames and arguments
  * After `$` → variable names
  * After `~` → user home names
