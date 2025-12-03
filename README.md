- [4. Variables and Environment](#variables-and-environment)
    - [4.1. Variables](#variables)
        - [4.1.1. Variable Naming](#variable-naming)
        - [4.1.2. The `set` Builtin](#the-set-builtin)
        - [4.1.3. The `unset` Builtin](#the-unset-builtin)
    - [4.2. Environment Variables](#environment-variables)
        - [4.2.1. The `export` Builtin](#the-export-builtin)
        - [4.2.2. The `unexport` Builtin](#the-unexport-builtin)
- [5. Aliases](#aliases)
    - [5.1. Alias Naming](#alias-naming)
    - [5.2. Alias Expansion](#alias-expansion)
    - [5.3. The `alias` Builtin](#the-alias-builtin)
    - [5.4. The `unalias` Builtin](#the-unalias-builtin)
- [6. Shell Expansions](#shell-expansions)
    - [6.1. Tilde Expansion](#tilde-expansion)
    - [6.2. Parameter Expansion](#parameter-expansion)
        - [6.2.1. Variable Expansion](#variable-expansion)
        - [6.2.2. Special Parameters](#special-parameters)
    - [6.3. Command Substitution](#command-substitution)
    - [6.4. Word Splitting](#word-splitting)
    - [6.5. Filename Expansion](#filename-expansion)
    - [6.6. Quote Removal](#quote-removal)

---

### 4.1 Variables <a name="variables"></a>

Shell variables are name-value pairs stored in the internal variables hash table.

> ℹ️ **Note:** Special shell parameters such as `$?`, `$$`, and `$!` are not
> regular variables and cannot be set or exported. They are read-only and only available through parameter expansion. See [Special Parameters](#special-parameters).

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

## 5. Aliases <a name="aliases"></a>

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

## 6. Shell Expansions <a name="shell-expansions"></a>

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

`xd-shell` supports the standard forms:

- `~` and `~/path`  
  Expands to the current user's home directory.  
  Uses `$HOME` if set, otherwise falls back to the passwd database.

- `~user` and `~user/path`  
  Expands to the home directory of `user`.  
  If `user` does not exist, no expansion occurs.

- `~+` and `~+/path`  
  Expands to the current working directory (`$PWD`).  
  If `PWD` is unset, no expansion occurs.

- `~-` and `~-/path`  
  Expands to the previous working directory (`$OLDPWD`).  
  If `OLDPWD` is unset, no expansion occurs.

---

### 6.2 Parameter Expansion <a name="parameter-expansion"></a>

Parameter expansion replaces references to shell variables, environment variables,
and special parameters with their values.

A `$` begins parameter expansion only when it appears in unquoted or
double-quoted text and is followed by a valid variable name, a supported special
parameter, or `{`. Otherwise, it does not start parameter expansion and the `$`
character is preserved.

---

#### 6.2.1 Variable Expansion <a name="variable-expansion"></a>

The following forms expand shell variables or environment variables:

- `$VAR`
- `${VAR}` — preferred when characters immediately following the variable name
  would otherwise be interpreted as part of it

If a variable is not set, it expands to an empty string.

---

#### 6.2.2 Special Parameters <a name="special-parameters"></a>

The following special parameters are supported:

- `$?` — exit status of the last command  
- `$$` — PID of the current shell  
- `$!` — PID of the most recent background job  

Braced forms such as `${?}` are also supported.

---

### 6.3 Command Substitution <a name="command-substitution"></a>

Command substitution allows the output of a command to replace the substitution
itself. It occurs when a command is enclosed as `$(command)`.

`xd-shell` performs command substitution by executing `command` in a subshell and
replacing the entire `$(...)` construct with the standard output of `command`, with
any trailing newline characters removed.

The text inside `$(...)` is parsed as a normal `xd-shell` command list. It may
contain pipelines, redirections, and nested substitutions.

Only the command's `stdout` is captured, `stderr` is left unchanged.

Because the command runs in a subshell, any side effects (such as variable
assignments or changing the working directory) do not affect the parent shell.

---

### 6.4 Word Splitting <a name="word-splitting"></a>

Word splitting is applied to the result of parameter expansion and command 
substitution when they occur outside of double quotes. The expanded text is split
into separate words based on the whitespace characters: space (` `), tab (`\t`),
and newline (`\n`). Consecutive whitespace characters act as a single separator.

---

### 6.5 Filename Expansion <a name="filename-expansion"></a>

After word splitting, `xd-shell` performs filename expansion on words that
contain unquoted wildcard characters or brace patterns. Filename expansion
replaces such patterns with the list of matching pathnames.

The following forms are supported:

| Syntax      | Description                                                |
|-------------|------------------------------------------------------------|
| `*`         | Matches any sequence of characters                         |
| `?`         | Matches any single character                               |
| `[abc]`     | Matches one character in the class                         |
| `[a-z]`     | Matches one character in the range                         |
| `[!abc]`    | Matches one character not in the class                     |
| `[!a-z]`    | Matches one character not in the range                     |
| `{a,b,c}`   | Brace expansion: expands to each alternative inside braces |

Filename expansion applies to each path component separately. A `/` cannot be
matched by `*`, `?`, or bracket expressions. Files beginning with `.` are only
matched when the pattern explicitly starts with a `.`.

If a pattern produces no matches, it is left unchanged.

> ℹ️ **Note:** brace expansion (`{a,b,c}`) is supported only during filename expansion.  

---

### 6.6 Quote Removal <a name="quote-removal"></a>

After all previous expansions, `xd-shell` removes all unquoted `\`, `'`, and `"` 
characters that did not result from one of the expansions.  
Backslashes are removed only when they are escaping another character (outside 
single quotes, and inside double quotes only for `$`, `"`, `\`, and `newline`).

---
