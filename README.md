- [4. Variables and Environment](#variables-and-environment)
    - [4.1. Shell Variables](#shell-variables)
        - [4.1.1. Variable Naming](#variable-naming)
        - [4.1.2. The `set` Builtin](#the-set-builtin)
        - [4.1.3. The `unset` Builtin](#the-unset-builtin)
    - [4.2. Environment Variables](#environment-variables)
        - [4.2.1. The `export` Builtin](#the-export-builtin)
        - [4.2.2. The `unexport` Builtin](#the-unexport-builtin)

---

## 4. Variables and Environment <a name="variables-and-environment"></a>

`xd-shell` maintains an internal hash table for storing shell variables and
environment variables. Both types share the same hash table, with each
variable carrying an exported flag that determines whether it is passed to 
child processes.

---

### 4.1 Shell Variables <a name="shell-variables"></a>

Shell variables are name-value pairs stored in the internal variables hash table.

---

#### 4.1.1 Variable Naming <a name="variable-naming"></a>

Shell variable names may consist of letters, digits, and underscores, but must
not begin with a digit. 

> ℹ️ **Note:**
> Variable names are case-sensitive.

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

Returns `0` unless invalid option is given or error occurs.

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

Returns `0` unless invalid option is given or error occurs.

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

Returns `0` unless invalid option is given or error occurs.

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

Returns `0` unless invalid option is given or error occurs.

---
