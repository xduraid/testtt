# XD-Shell

## Table of Contents

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
- [3 Shell Language](#shell-language)
    - [3.1 Commands](#commands)
    - [3.2 Pipelines](#pipelines)
    - [3.3 Comments](#comments)
    - [3.4 Quoting and Escaping](#quoting-and-escaping)
        - [3.4.1 Single Quotes](#single-quotes)
        - [3.4.2 Double Quotes](#double-quotes)
        - [3.4.3 Escape Sequences](#escape-sequences)
        - [3.4.4 Line Continuation](#line-continuation)
    - [3.5 Redirections](#redirections)
- [4 Variables and Environment](#variables-and-environment)
    - [4.1 Variables](#variables)
        - [4.1.1 Variable Naming](#variable-naming)
        - [4.1.2 The `set` Builtin](#the-set-builtin)
        - [4.1.3 The `unset` Builtin](#the-unset-builtin)
    - [4.2 Environment Variables](#environment-variables)
        - [4.2.1 The `export` Builtin](#the-export-builtin)
        - [4.2.2 The `unexport` Builtin](#the-unexport-builtin)
- [5 Aliases](#aliases)
    - [5.1 Alias Naming](#alias-naming)
    - [5.2 Alias Expansion](#alias-expansion)
    - [5.3 The `alias` Builtin](#the-alias-builtin)
    - [5.4 The `unalias` Builtin](#the-unalias-builtin)
- [6 Shell Expansions](#shell-expansions)
    - [6.1 Tilde Expansion](#tilde-expansion)
    - [6.2 Parameter Expansion](#parameter-expansion)
        - [6.2.1 Variable Expansion](#variable-expansion)
        - [6.2.2 Special Parameters](#special-parameters)
    - [6.3 Command Substitution](#command-substitution)
    - [6.4 Word Splitting](#word-splitting)
    - [6.5 Filename Expansion](#filename-expansion)
    - [6.6 Quote Removal](#quote-removal)
- [7 Job Control](#job-control)
    - [7.1 Foreground and Background Jobs](#foreground-and-background-jobs)
    - [7.2 Job States](#job-states)
    - [7.3 Job Specifiers](#job-specifiers)
    - [7.4 The `jobs` Builtin](#the-jobs-builtin)
    - [7.5 The `fg` Builtin](#the-fg-builtin)
    - [7.6 The `bg` Builtin](#the-bg-builtin)
    - [7.7 The `kill` Builtin](#the-kill-builtin)

---

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

## 3 Shell Language <a name="shell-language"></a>

`xd-shell` provides a command language supporting command pipelines,
I/O redirections, background execution, and standard quoting and escaping rules.

---

### 3.1 Commands <a name="commands"></a>

A command in `xd-shell` consists of a command name (a builtin or external
program), zero or more arguments, and optional I/O redirections  (see
[Redirections](#redirections)).

**General Form:**

```text
command [arg ...] [redirection ...]
```

---

### 3.2 Pipelines <a name="pipelines"></a>

A pipeline connects the standard output of one command to the standard input of
the next command using the `|` operator.

**General Form:**

```text
cmd [| cmd ...] [&] <newline>
```

**Rules and Behavior:**

- A pipeline must be terminated by a newline (`\n`) or end-of-file (`EOF`).
- A pipeline must appear entirely on one logical line unless extended using a
  line continuation (`\`).
- Each command in the pipeline runs in parallel as a separate process.
- The exit status of the pipeline is the exit status of the last command.
- The optional `&` may appear only after the final command to run the entire
  pipeline in the background.

---

### 3.3 Comments <a name="comments"></a>

A comment begins with the `#` character and continues to the end of the line.
It is recognized only when the `#` appears outside of single quotes `'...'` or
double quotes `"..."`.

Everything from the `#` character up to the next newline is ignored by the shell.

---

### 3.4 Quoting and Escaping <a name="quoting-and-escaping"></a>

Quoting controls how characters are interpreted by the shell. `xd-shell`
supports single quotes, double quotes, backslash escaping, and line continuation.

---

#### 3.4.1 Single Quotes <a name="single-quotes"></a>

Enclosing characters in single quotes preserves the literal value of every
character inside the quotes. A single quote may not appear inside a single-quoted
string, even when preceded by a backslash.

---

#### 3.4.2 Double Quotes <a name="double-quotes"></a>

Enclosing characters in double quotes preserves the literal value of all
characters except `$`, `"`, and `\`.

Inside double quotes, a backslash may be used to remove the special meaning of
the characters `$`, `"`, `\`, or `\n`. When a backslash appears before one
of these characters, the backslash is removed and the following character is
preserved literally. A backslash preceding any other character inside double 
quotes is left unchanged.

---

#### 3.4.3 Escape Sequences <a name="escape-sequences"></a>

Outside of quotes, a backslash (`\`) preserves the literal value of the character
that follows it, removing any special meaning that character would otherwise
have. If the character following the backslash has no special meaning, the backslash
is simply removed and the character is used unchanged.

---

#### 3.4.4 Line Continuation <a name="line-continuation"></a>

A backslash (`\`) immediately followed by `\n` is treated as a line continuation.
Both characters are removed, allowing a command to span multiple physical lines.

---

### 3.5 Redirections <a name="redirections"></a>

Redirections change the source of standard input (`stdin`) or the destination of
standard output (`stdout`) and standard error (`stderr`).

In `xd-shell`, redirections must appear **after all command arguments**. Any word
following a redirection operator is treated as the redirection target and not as
a normal argument.

`xd-shell` supports the following redirections:

| Redirection   | Description                                              |
|---------------|----------------------------------------------------------|
| `< file`      | Redirect `stdin` from *file*                             |
| `> file`      | Redirect `stdout` to *file* (truncate)                   |
| `>> file`     | Redirect `stdout` to *file* (append)                     |
| `2> file`     | Redirect `stderr` to *file* (truncate)                   |
| `2>> file`    | Redirect `stderr` to *file* (append)                     |
| `>& file`     | Redirect both `stdout` and `stderr` to *file* (truncate) |
| `>>& file`    | Redirect both `stdout` and `stderr` to *file* (append)   |

Redirections are processed from left to right. When multiple redirections modify
the same stream, the last one takes effect.

> ℹ️ **Note:** Only filenames (or words that expand to filenames) may be used as
redirection targets.

---

## 4 Variables and Environment <a name="variables-and-environment"></a>

`xd-shell` maintains an internal hash table for storing shell variables and
environment variables. Both types share the same hash table, with each
variable carrying an exported flag that determines whether it is passed to 
child processes.

---

### 4.1 Variables <a name="variables"></a>

Shell variables are name-value pairs stored in the internal variables hash table.

---

#### 4.1.1 Variable Naming <a name="variable-naming"></a>

Shell variable names may consist of letters, digits, and underscores, but must
not begin with a digit. 

> ℹ️ **Note:** variable names are case-sensitive.

---

#### 4.1.2 The `set` Builtin <a name="the-set-builtin"></a>

The `set` builtin is used to assign values to variables or display existing ones.

**Usage:**

```sh
set [name[=value] ...]
```

**Options:**

| Option       | Description            |
|--------------|------------------------|
| `--help`     | Show help information  |

**Behavior:**

- **Without arguments:**  
  Prints all defined variables in the following reusable form:  
  ```sh
  set name1='value1'
  set name2='value2'
  ...
  set nameN='valueN'
  ```

- **With arguments:**  
  Each argument may be one of the following:
  - `name` — prints the variable `name` in the reusable form.
  - `name=value` — sets the variable `name` to `value`, preserving its exported status.

**Exit status:**

Returns `0` unless an invalid option is given or an error occurs.

---

#### 4.1.3 The `unset` Builtin <a name="the-unset-builtin"></a>

The `unset` builtin is used to unset variables.

**Usage:**

```sh
unset name [name ...]
```

**Options:**

| Option       | Description            |
|--------------|------------------------|
| `--help`     | Show help information  |

**Behavior:**

For each argument `name`, it unsets the variable `name`.

**Exit status:**

Returns `0` unless an invalid option is given or an error occurs.

---

### 4.2 Environment Variables <a name="environment-variables"></a>

Environment variables are stored in the same internal hash table as shell
variables, but have the exported flag set.   
Exported variables are included in the environment of commands executed by the shell.

---

#### 4.2.1 The `export` Builtin <a name="the-export-builtin"></a>

The `export` builtin is used to mark variables as exported so they are included in
the environment of executed commands.

**Usage:**

```sh
export [-p] [name[=value] ...]
```

**Options:**

| Option   | Description                               |
|----------|-------------------------------------------|
| `-p`     | Print exported variables in reusable form |
| `--help` | Show help information                     |

**Behavior:**

- **Without arguments or with `-p`:**  
  Prints all exported variables in the following reusable form:  
  
  ```sh
  export name1='value1'
  export name2='value2'
  ...
  export nameN='valueN'
  ```

- **With arguments:**  
  Each argument may be one of the following:
  - `name` — marks the variable `name` as exported.
  - `name=value` — sets the variable `name` to `value` and marks it as exported.

**Exit status:**

Returns `0` unless an invalid option is given or an error occurs.

---

#### 4.2.2 The `unexport` Builtin <a name="the-unexport-builtin"></a>

The `unexport` builtin is used to mark variables as not exported so they are no longer included in the environment of executed commands.

**Usage:**

```sh
unexport name [name ...]
```

**Options:**

| Option   | Description           |
|----------|-----------------------|
| `--help` | Show help information |


**Behavior:**

For each argument `name`, it clears the exported flag of the variable `name`.

**Exit status:**

Returns `0` unless an invalid option is given or an error occurs.

---

## 5 Aliases <a name="aliases"></a>

Aliases are used to substitute a command name with a string before the command is
parsed.  
`xd-shell` maintains an internal hash table for storing command aliases.

---

### 5.1 Alias Naming <a name="alias-naming"></a>

Alias names follow the same rules as shell variable names. Alias names may consist 
of letters, digits, and underscores, but must not begin with a digit. 

> ℹ️ **Note:** Alias names are case-sensitive.

---

### 5.2 Alias Expansion <a name="alias-expansion"></a>

`xd-shell` performs alias expansion by checking the first word of each scanned
command. If this word is unquoted and matches an alias, it is replaced with its
alias value before the command is parsed.

---

### 5.3 The `alias` Builtin <a name="the-alias-builtin"></a>

The `alias` builtin is used to define aliases or display existing ones.

**Usage:**

```sh
alias [name[=value] ...]
```

**Options:**

| Option   | Description           |
|----------|-----------------------|
| `--help` | Show help information |

**Behavior:**

- **Without arguments:**  
  Prints all defined aliases in the following reusable form:  

  ```sh
  alias name1='value1'
  alias name2='value2'
  ...
  alias nameN='valueN'
  ```

- **With arguments:**  
  Each argument may be one of the following:
  - `name` — prints the alias `name` in the reusable form.
  - `name=value` — defines or redefines the alias `name` with the replacement 
  string `value`.

**Exit status:**

Returns `0` unless an invalid option is given or an error occurs.

---

### 5.4 The `unalias` Builtin <a name="the-unalias-builtin"></a>

The `unalias` builtin is used to undefine aliases.

**Usage:**

```sh
unalias name [name ...]
```

**Options:**

| Option   | Description           |
|----------|-----------------------|
| `--help` | Show help information |

**Behavior:**

For each argument `name`, it undefines the alias `name`.

**Exit status:**

Returns `0` unless an invalid option is given or an error occurs.

---

## 6 Shell Expansions <a name="shell-expansions"></a>

Before executing a command, `xd-shell` applies a series of expansions to each
word.  
These expansions are done in the following order:

1. [Tilde Expansion](#tilde-expansion)
2. [Parameter Expansion](#parameter-expansion)
3. [Command Substitution](#command-substitution)
4. [Word Splitting](#word-splitting)
5. [Filename Expansion](#filename-expansion)
6. [Quote Removal](#quote-removal)

---

### 6.1 Tilde Expansion <a name="tilde-expansion"></a>

Tilde expansion occurs when a word begins with `~` and is not quoted.  
`xd-shell` supports the following forms of tilde expansion:

- `~` and `~/path`  
  Expands `~` to the current user's home directory.  
  Uses `$HOME` if set, otherwise falls back to the passwd database.

- `~user` and `~user/path`  
  Expands `~user` to the home directory of the user `user`.  
  If the user `user` does not exist, no expansion occurs.

- `~+` and `~+/path`  
  Expands `~+` to the current working directory (`$PWD`).  
  If `PWD` is unset, no expansion occurs.

- `~-` and `~-/path`  
  Expands `~-` to the previous working directory (`$OLDPWD`).  
  If `OLDPWD` is unset, no expansion occurs.

---

### 6.2 Parameter Expansion <a name="parameter-expansion"></a>

Parameter expansion replaces references to shell variables, environment variables,
and special parameters with their values.

A `$` begins parameter expansion only when it appears in unquoted or
double-quoted text and is followed by a valid variable name, a supported special
parameter, or `{`. Otherwise, it does not start parameter expansion and the `$`
character is preserved.

`xd-shell` supports the following forms of parameter expansion:

- `$NAME`
- `${NAME}` — preferred to prevent characters immediately following the variable name from being interpreted as part of it.

`NAME` may refer to a shell variable, an environment variable, or a special parameter.

---

#### 6.2.1 Variable Expansion <a name="variable-expansion"></a>

When `NAME` in `$NAME` or `${NAME}` refers to a variable, parameter expansion
replaces it with its value. If the variable is not defined, it is replaced with
an empty string.

---

#### 6.2.2 Special Parameters <a name="special-parameters"></a>

When `NAME` in `$NAME` or `${NAME}` refers to a special parameter, parameter
expansion replaces it with its value.

`xd-shell` supports the following special parameters:

- `?` — the exit status of the last command
- `$` — the PID of the current shell
- `!` — the PID of the most recent background job

---

### 6.3 Command Substitution <a name="command-substitution"></a>

Command substitution allows the output of a command to replace the command
itself. It occurs when a command is enclosed as `$(command)`.

`xd-shell` performs command substitution by executing `command` in a subshell and
replacing the entire `$(...)` construct with the standard output of `command`, with
any trailing newline characters removed.

The text inside `$(...)` is parsed as a normal `xd-shell` command list, it may
contain pipelines, redirections, and nested substitutions.

Only the command's `stdout` is captured, `stderr` is left unchanged.

> ℹ️ **Note:** Because the command in command substitution runs in a subshell, any 
side effects such as variable assignments or changing the working directory do not 
affect the parent shell.

---

### 6.4 Word Splitting <a name="word-splitting"></a>

`xd-shell` applies word splitting to the result of parameter expansion and command 
substitution when they occur outside of double quotes. The expanded text is split
into separate words based on the whitespace characters: space (` `), tab (`\t`),
and newline (`\n`). Consecutive whitespace characters act as a single separator.

---

### 6.5 Filename Expansion <a name="filename-expansion"></a>

After word splitting, `xd-shell` performs filename expansion on words that
contain unquoted wildcard characters, bracket expressions, or brace patterns.
Filename expansion replaces these constructs with the list of files or
directories whose names match the pattern.

`xd-shell` supports the following constructs in filename expansion:

| Syntax      | Description                                                                  |
|-------------|------------------------------------------------------------------------------|
| `*`         | Matches any sequence of characters                                           |
| `?`         | Matches exactly one character                                                |
| `[abc]`     | Matches a single character from those listed inside the brackets             |
| `[a-z]`     | Matches a single character from the specified character range                |
| `[!abc]`    | Matches a single character not listed inside the brackets                    |
| `[!a-z]`    | Matches a single character outside the specified character range             |
| `{a,b,c}`   | Brace expansion: expands into each alternative before matching occurs        |


Filename expansion is applied to each path component separately. The `/` charater cannot be matched by `*`, `?`, or bracket expressions. Additionally, files beginning with `.` are only matched when the pattern explicitly starts with a `.`.

If a pattern does not match any files or directories, no expansion is performed
and the word is left unchanged.

---

### 6.6 Quote Removal <a name="quote-removal"></a>

After all previous expansions, `xd-shell` removes all unquoted `\`, `'`, and `"`
characters that did not result from an expansion.  
A backslash is removed outside quotes, and inside double quotes when it appears before `$`, `"`, `\`, 
or `\n`.

---

## 7 Job Control <a name="job-control"></a>

A job in `xd-shell` is a single command or a pipeline of commands that are
executed together as a unit. All processes belonging to the same job share a
*process group ID (PGID)*, and every active job is assigned a *job ID* that the
shell uses to identify and manage it through job-control builtins.

`xd-shell` provides a job control system when running interactively in a terminal,
enabling users to run jobs in the foreground or background and to suspend or
resume them as needed.

---

### 7.1 Foreground and Background Jobs <a name="foreground-and-background-jobs"></a>

In interactive mode, `xd-shell` manages terminal ownership by deciding which
process group controls the terminal. At any moment, either the shell or the job
running in the foreground holds the terminal, ensuring that input and
terminal-generated signals are delivered correctly.

By default, a job runs in the foreground. The shell gives it control of the
terminal and waits for it to finish or become stopped before reading the next
command. Foreground jobs receive terminal-generated signals such as *SIGINT*
(`Ctrl+C`) and *SIGTSTP* (`Ctrl+Z`).

A job can also be started in the background by appending `&` to the command
line. Background jobs do not take control of the terminal; the shell
immediately returns to the prompt so other commands can be entered while the job
continues running. Background jobs do not receive terminal-generated signals,
and if they attempt to read from or write to the terminal, they are stopped by
the operating system through *SIGTTIN* or *SIGTTOU*.

> ℹ️ **Note:** In interactive mode, `xd-shell` ignores several signals that are 
intended for foreground jobs rather than for the shell itself, including *SIGTERM*, 
*SIGINT*, *SIGQUIT*, *SIGTSTP*, *SIGTTIN*, and *SIGTTOU*. This prevents the shell 
from being interrupted or suspended.

---

### 7.2 Job States <a name="job-states"></a>

`xd-shell` tracks the state of each active job so it can report changes and
manage jobs correctly during execution.

A job may be in one of the following states:

- **Running** — the job is currently executing.
- **Stopped** — the job has been suspended.
- **Done** — the job finished with an exit status of `0`.
- **Exited** — the job finished with a non-zero exit status.
- **Signaled** — the job was terminated by a signal.

---

### 7.3 Job Specifiers <a name="job-specifiers"></a>

`xd-shell` allows referring to jobs using job specifiers, which are short
notations beginning with `%`. These are used by job-control builtins to identify
which job should be acted on.

Supported job specifiers:

- `%n` — job with ID `n`
- `%+` or `%%` — the *current job*
- `%-` — the *previous job*

After each command completes or a job changes state, `xd-shell` refreshes the job
table and updates the current and previous jobs. The *current job* is the most
recently active job, with stopped jobs preferred over running ones. The *previous
job* is simply the next most recently active job.

---

### 7.4 The `jobs` Builtin <a name="the-jobs-builtin"></a>

The `jobs` builtin is used to display the status of all active jobs.

**Usage:**

```sh
jobs [-lp]
```

**Options:**

| Option   | Description                                      |
|----------|--------------------------------------------------|
| `-l`     | Show detailed status for each process in the job |
| `-p`     | Show the process ID(s) associated with each job  |
| `--help` | Show help information                            |

**Behavior:**

The `jobs` builtin does not accept any non-option arguments. It prints the
current job table, and the `-l` and `-p` options modify which additional
information is included.

**Exit status:**

Returns `0` unless an invalid option is given or an error occurs.

---

### 7.5 The `fg` Builtin <a name="the-fg-builtin"></a>

The `fg` builtin is used to move a job into the foreground, resuming it if it is 
stopped.

**Usage:**

```sh
fg [job_spec]
```

**Options:**

| Option   | Description            |
|----------|------------------------|
| `--help` | Show help information  |


**Behavior:**

The `fg` builtin only works when job control is enabled in interactive shell mode. 
It accepts at most one optional `job_spec` operand. If `job_spec`
is omitted, the *current job* (as described in [Job Specifiers](#job-specifiers)) is 
used. The specified job is brought into the foreground, if it was stopped, it is 
resumed and runs in the foreground while `xd-shell` waits for it to finish or stop 
again.

**Exit status:**

Returns the exit status of the command placed in the foreground unless an error occurs.

---

### 7.6 The `bg` Builtin <a name="the-bg-builtin"></a>

The `bg` builtin is used to move one or more jobs to the background, resuming them 
if they are stopped.

**Usage:**

```sh
bg [job_spec ...]
```

**Options:**

| Option   | Description           |
|----------|-----------------------|
| `--help` | Show help information |

**Behavior:**

The `bg` builtin only works when job control is enabled in interactive shell
mode. It accepts zero or more `job_spec` operands. If no `job_spec` is given,
the *current job* (as described in [Job Specifiers](#job-specifiers)) is 
used. Each specified job is moved to the background, if it was stopped, it is 
resumed and continues running in the background as if it had been started with `&`.

**Exit status:**

Returns `0` unless job control is not enabled or an error occurs.

---

### 7.7 The `kill` Builtin <a name="the-kill-builtin"></a>

The `kill` builtin is used to send a signal to one or more processes or jobs.

**Usage:**

```sh
kill [-s sigspec | -n signum] pid | jobspec ...
kill -l
```

**Options:**

| Option   | Description                                           |
|----------|-------------------------------------------------------|
| `-s sig` | Use the signal named by `sig`                         |
| `-n num` | Use the signal numbered `num`                         |
| `-l`     | List the available signal names and their numbers     |
| `--help` | Show help information                                 |

**Behavior:**

By default, `kill` sends the `SIGTERM` signal. When `-s` or `-n` is provided, the
specified signal name or number determines which signal is sent. Each argument is
either a process ID or a job specifier beginning with `%` (as described in
[Job Specifiers](#job-specifiers)). When a job specifier is used, the signal is
sent to the job's process group. With `-l`, `kill` prints the list of available
signals and their numbers instead of sending a signal.

**Exit status:**

Returns `0` unless an invalid option is given or an error occurs.

---
