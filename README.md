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

---

## 3 Shell Language <a name="shell-language"></a>

`xd-shell` provides a command language supporting command pipelines,
I/O redirections, background execution, and standard quoting and escaping rules.

---

## 3.1 Commands <a name="commands"></a>

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
| `< file`      | Redirect `stdin` from `file`                             |
| `> file`      | Redirect `stdout` to `file` (truncate)                   |
| `>> file`     | Redirect `stdout` to `file` (append)                     |
| `2> file`     | Redirect `stderr` to `file` (truncate)                   |
| `2>> file`    | Redirect `stderr` to `file` (append)                     |
| `>& file`     | Redirect both `stdout` and `stderr` to `file` (truncate) |
| `>>& file`    | Redirect both `stdout` and `stderr` to `file` (append)   |

Redirections are processed from left to right. When multiple redirections modify
the same stream, the last one takes effect.

> ℹ️ **Note:** Only filenames (or words that expand to filenames) may be used as
redirection targets.

---
