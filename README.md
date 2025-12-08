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
    - [7.3 Terminal Control](#terminal-control)
    - [7.4 Signal Handling](#signal-handling)
    - [7.5 Job Specifiers](#job-specifiers)
    - [7.6 The `jobs` Builtin](#the-jobs-builtin)
    - [7.8 The `bg` Builtin](#the-bg-builtin)
    - [7.9 The `kill` Builtin](#the-kill-builtin)

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

#### 5.3 The `alias` Builtin <a name="the-alias-builtin"></a>

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

#### 5.4 The `unalias` Builtin <a name="the-unalias-builtin"></a>

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

A job in `xd-shell` is a single command or a pipeline of commands that are executed together as a unit.  
All processes belonging to the same job share a process group ID (PGID), allowing them to be started, stopped, resumed, or signaled together.

`xd-shell` provides a job control system when running interactively in a terminal. 
It allows the shell to run commands in the foreground or background, suspend and 
resume running processes, and deliver terminal signals (such as `Ctrl+C` or 
`Ctrl+Z`) to the appropriate group of processes.

---

### 7.1 Foreground and Background Jobs <a name="foreground-and-background-jobs"></a>

When a job is executed in `xd-shell`, it normally runs in the foreground.
Foreground jobs take control of the terminal, receive keyboard input, and the
shell waits for them to finish before reading the next command.

A job can also be started in the background by appending `&` to the end of the
command line. Background jobs run without taking control of the terminal. The
shell returns to the prompt immediately, allowing additional commands to be
entered while the job continues executing in the background. Each background job
is assigned a job ID, which allows it to be tracked and managed by the shell.

---

### 7.2 Job States <a name="job-states"></a>

`xd-shell` tracks the state of each active job so it can report state changes and
manage jobs correctly during execution. 

A job may be in one of the following states:

- Running – the job is still executing.
- Stopped – the job has been suspended.
- Done – the job finished normally with an exit status of 0.
- Exited – the job finished with a non-zero exit status.
- Signaled – the job ended because it received a terminating signal.

These states allow the shell to provide meaningful job listings, resume stopped
jobs, and clean up completed ones.

---

### 7.3 Terminal Control <a name="terminal-control"></a>

When running interactively, `xd-shell` manages which process group controls the
terminal.  
Foreground jobs are given control of the terminal while running, allowing them to receive keyboard input and terminal-generated signals (`Ctrl+C`, `Ctrl+Z`, 
etc.). 
The shell regains control of the terminal whenever the foreground job stops or finishes. 

---

### 7.4 Signal Handling <a name="signal-handling"></a>

`xd-shell` manages several signals to support interactive job control. When a job
runs in the foreground, terminal-generated signals such as `Ctrl+C` (SIGINT) and
`Ctrl+Z` (SIGTSTP) are delivered to that job. Background jobs do not receive
these signals. The shell itself ignores signals that would otherwise terminate or
stop it, ensuring that only the foreground job is affected. When a job stops or
finishes, xd-shell detects this through SIGCHLD and updates its job table
accordingly.

---

### 7.5 Job Specifiers <a name="job-specifiers"></a>

`xd-shell` allows referring to jobs using job specifiers, which are short
notations beginning with `%`. These are used by job-control builtins to identify 
which job should be acted on.

Supported job specifiers:

- `%n` — job with ID `n`
- `%+` or `%%` — the *current job*
- `%-` — the *previous job*

After each command completes or a job changes state, `xd-shell` refreshes the jobs
table and updates the current and previous jobs. The *current job* is the most 
recently active job, with stopped jobs taking priority over running ones. The
*previous job* is simply the next most recently active job.

---

### 7.6 The `jobs` Builtin <a name="the-jobs-builtin"></a>

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

The `jobs` builtin does not accept any non-option arguments. It always prints the
current job table, and the `-l` and `-p` options modify which additional
information is included.

**Exit status:**

Returns `0` unless an invalid option is given or an error occurs.

---

### 7.7 The `fg` Builtin <a name="the-fg-builtin"></a>

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
is omitted, the shell's *current job* (as described in [Job Specifiers](#job-specifiers)) is used. The specified job is brought into the foreground, if it 
was stopped, it is resumed and run in the foreground while `xd-shell` waits for it 
to finish or stop again.

**Exit status:**

Returns the exit status of the command placed in foreground unless an error occurs.

---

### 7.8 The `bg` Builtin <a name="the-bg-builtin"></a>

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
the shell's *current job* (as described in [Job Specifiers](#job-specifiers)) is 
used. Each specified job is moved to the background, if it was stopped, it is 
resumed and continues running in the background as if it had been started with `&`.

**Exit status:**

Returns `0` unless job control is not enabled or an error occurs.

---

### 7.9 The `kill` Builtin <a name="the-kill-builtin"></a>

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
