---

## 4. Variables and Environment <a name="variables-and-environment"></a>

`xd-shell` maintains an internal hash table for storing shell variables and
environment variables. Both types share the same underlying table, with each
variable carrying an exported flag that determines whether it is passed to 
child processes.

---

### 4.1 Shell Variables <a name="shell-variables"></a>

Shell variables are name-value pairs stored in the shell's internal variable hash 
table.

---

#### 4.1.1 Variable Naming <a name="variable-naming"></a>

Shell variable names may consist of letters, digits, and underscores, but must
not begin with a digit. Variable names are case-sensitive.

---

#### 4.1.2 The `set` Builtin <a name="the-set-builtin"></a>

The `set` builtin is used to assign values to variables or display existing ones.

**Usage:**

```bash
set [name[=value] ...]
```

**Options:**

| Option       | Description            |
|--------------|------------------------|
| `--help`     | Show help information  |

**Behavior:**

- **Without arguments:**  
  Prints all defined variables in the following reusable form:  
  ```bash
    set name1='value1'
    set name2='value2'
    .
    .
    set nameN='valueN'
    ```

- **With arguments:**  
  Each argument may be one of the following:
  - `name` — prints the value of the variable `name`.
  - `name=value` — assigns or updates the variable `name` (preserving its exported status).

**Exit code:**

Returns `0` if all operations succeed, and a non-zero value if any invalid name,
unknown variable, or invalid option is encountered.

---
