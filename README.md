- [2 Interactive Shell Mode](#interactive-shell-mode)
    - [2.1 Prompt Format](#input-prompt)
    - [2.2 Readline Features](#readline-features)
        - [2.2.1 Line Editing and Cursor Movement](#line-editing)
        - [2.2.2 History Navigation](#history-navigation)
        - [2.2.3 History Search](#history-search)
        - [2.2.4 Tab Key](#tab-key)
    - [2.3 Command History](#command-history)
        - [2.3.1 In-Memory History](#in-memory-history)
        - [2.3.2 Persistent History File](#persistent-history-file)
        - [2.3.3 The `history` Builtin](#the-history-builtin)
    - [2.4 Tab Completion](#tab-completion)
        - [2.4.1 Completion Types](#completion-types)
        - [2.4.2 Completion Behavior](#completion-behavior)
--

## 2 Interactive Shell Mode <a name="interactive-shell-mode"></a>

`xd-shell` provides an interactive environment when both `stdin` and `stdout` are 
connected to a terminal. Interactive mode enables a dynamic prompt, line editing, 
command history, tab completion, and job control.

---

### 2.1 Input Prompt <a name="input-prompt"></a>

In interactive mode, `xd-shell` displays a dynamic, colorized prompt that includes
the current user, host, and working directory. The prompt has the form
`user@host:cwd$`, ending with `$` for normal users and `#` for root. The `$HOME`
prefix in the working directory is collapsed to `~` when the directory is inside
the user's home, and the working directory shown in the prompt is updated before
each input line to match the actual current directory.

---

### 2.2 Readline Features <a name="readline-features"></a>

`xd-shell` uses [`xd-readline`](https://github.com/xduraid/xd-readline), a custom 
line-editing library implemented specifically for this project.  
It provides interactive features such as line editing, command history, and tab 
completion.

---

#### 2.2.1 Line Editing and Cursor Movement <a name="line-editing"></a>

`xd-shell` supports full editing of the input line at any cursor position. You can
move the cursor by character or word, jump to the beginning or end of the line,
and delete characters or words efficiently. 

The following cursor-movement and editing actions are supported:

| Key Combination          | Action                                                 |
|--------------------------|--------------------------------------------------------|
| `←` or `Ctrl+B`          | Move cursor one character to the left                  |
| `→` or `Ctrl+F`          | Move cursor one character to the right                 |
| `Ctrl+←` or `Alt+B`      | Move cursor one word to the left                       |
| `Ctrl+→` or `Alt+F`      | Move cursor one word to the right                      |
| `Home` or `Ctrl+A`       | Move cursor to the beginning of the line               |
| `End` or `Ctrl+E`        | Move cursor to the end of the line                     |
| `Backspace` or `Ctrl+H`  | Delete character before the cursor                     |
| `Delete`                 | Delete character at the cursor                         |
| `Ctrl+D`                 | Delete character at the cursor, or send `EOF` if empty |
| `Alt+Backspace`          | Delete word before the cursor                          |
| `Alt+D` or `Ctrl+Delete` | Delete word after the cursor                           |
| `Ctrl+U`                 | Delete everything before the cursor                    |
| `Ctrl+K`                 | Delete everything from the cursor to the end           |
| `Ctrl+L`                 | Clear the screen                                       |
| `Enter` or `Ctrl+J`      | Submit the current input line                          |

---

#### 2.2.2 History Navigation <a name="history-navigation"></a>

`xd-shell` allows navigating command history using dedicated keyboard shortcuts.
You can move backward and forward through previously executed commands, or jump
directly to the oldest or most recent entries.

The following history-navigation actions are supported:


| Key Combination              | Action                                       |
|------------------------------|----------------------------------------------|
| `↑` or `Page Up`             | Move to the previous history entry           |
| `↓` or `Page Down`           | Move to the next history entry               |
| `Ctrl+↑` or `Ctrl+Page Up`   | Jump to the first (oldest) history entry     |
| `Ctrl+↓` or `Ctrl+Page Down` | Jump to the last (most recent) history entry |

---

#### 2.2.3 History Search <a name="history-search"></a>

`xd-shell` provides real-time, incremental searching through the command history.
You can search backward or forward, and the matching history line updates as you
type. The current match is highlighted and can be accepted, skipped, or canceled.

The following history-search actions are supported:

| Key Combination | Action                                                |
|-----------------|-------------------------------------------------------|
| `Ctrl+R`        | Start reverse search or jump to the previous match    |
| `Ctrl+S`        | Start forward search or jump to the next match        |
| `Ctrl+G`        | Cancel the search and restore the original input line |
| `Esc Esc`       | Accept the current match and exit search mode         |

---

#### 2.2.4 Tab Key <a name="tab-key"></a>

`xd-shell` integrates with `xd-readline` to support tab completions. At this
level, the editor detects `Tab` presses and provides the interactive behavior
around completion requests, including recognizing double-`Tab` and displaying
possible completions. The completion rules themselves are described in the
[Tab Completion](#tab-completion) section below.

---

### 2.3 Command History <a name="command-history"></a>

`xd-shell` maintains both in-memory and persistent command history. History is
automatically loaded at startup, used during the session, and saved when the
shell exits.

---

#### 2.3.1 In-Memory History <a name="in-memory-history"></a>

During an interactive session, every executed command is appended to an internal
history list. Consecutive identical commands are not duplicated, and commands
that begin with leading whitespace are not added. The history list holds up to
1000 entries, discarding the oldest ones when the limit is reached. Recalled
history lines can be edited or re-executed, and the in-memory list is used
directly by history navigation and incremental search.

---

#### 2.3.2 Persistent History File <a name="persistent-history-file"></a>

When the shell starts, it attempts to load command history from the file
specified by `$HISTFILE`. If `$HISTFILE` is unset, `xd-shell` first sets it to
`$HOME/.xdsh_history` and then attempts to load the history file. When the shell
exits, the entire in-memory history list is written to the file indicated by
`$HISTFILE`, unless the variable was unset during the session.

---
#### 2.3.3 The `history` Builtin <a name="the-history-builtin"></a>

The `history` builtin is used to display and manipulate the shell's command
history.

**Usage:**

```sh
history [-c]  
history -a [file]  
history -r [file]  
history -w [file]  
history --help
```

**Options:**

| Option      | Description                                                         |
|-------------|---------------------------------------------------------------------|
| `-c`        | Clear the in-memory history list                                    |
| `-a [file]` | Append history entries from this session to *file*                  |
| `-r [file]` | Read *file* and append its contents to the in-memory history list   |
| `-w [file]` | Write the entire in-memory history list to *file*, overwriting it   |
| `--help`    | Show help information                                               |

If *file* is not given, `$HISTFILE` is used.

**Behavior:**

The `history` builtin only works in interactive shell mode. With no options, it
prints the current in-memory history list with line numbers. With `-c`, it
clears the in-memory list. The `-a`, `-r`, and `-w` options append, read, or
write history using the specified file (or `$HISTFILE` if no file is given). If
`-c` is combined with one of these options, the history list is cleared before
the file operation is performed. When multiple of `-a`, `-r`, or `-w` are given,
the last one takes effect.

**Exit status:**

Returns `0` unless an invalid option is given or an error occurs.

---

### 2.4 Tab Completion <a name="tab-completion"></a>

`xd-shell` provides context-aware tab completion for commands, paths, variables,
and other shell words. When `Tab` is pressed, the shell examines the word at the
cursor and generates a list of possible completions.

---

#### 2.4.1 Completion Types <a name="completion-types"></a>

`xd-shell` completes different kinds of words depending on context. The following
types of completions are supported:

| Type                    | Description                                                           |
| ----------------------- | --------------------------------------------------------------------- |
| **Executables**         | Commands found in directories listed in `$PATH`.                      |
| **Builtins**            | Shell builtins such as `cd`, `alias`, `history`, etc.                 |
| **Aliases**             | Alias names defined using the `alias` builtin.                        |
| **Variables**           | Shell and environment variable names.                                 |
| **Files & Directories** | Pathnames relative to the current directory or a typed prefix.        |
| **`~` / `~user`**       | Home directory expansions for the current user or other system users. |

Directories are completed with a trailing `/` when appropriate.

---

#### 2.4.2 Completion Behavior <a name="completion-behavior"></a>

When completion is requested, `xd-shell` determines the type of completion to
perform based on the word at the cursor and its context. It then generates the
set of possible completions. The context determines what is completed, such as
commands at the beginning of a line, pathnames after most commands, variable
names after `$` or `${`, or user home names after `~`.

---
