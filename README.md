- [7 Job Control](#job-control)
    - [7.1 Job States](#job-states)
    - [7.2 Foreground and Background Jobs](#foreground-and-background-jobs)
    - [7.3 Job Specifiers](#job-specifiers)
    - [7.4 The `jobs` Builtin](#the-jobs-builtin)
    - [7.5 The `fg` Builtin](#the-fg-builtin)
    - [7.6 The `bg` Builtin](#the-bg-builtin)
    - [7.7 The `kill` Builtin](#the-kill-builtin)

---

## 7 Job Control <a name="job-control"></a>

A job in `xd-shell` is a single command or a pipeline of commands that are
executed together as a unit. All processes belonging to the same job share a
process group ID (PGID), and every active job is assigned a job ID that the
shell uses to identify and manage it through job-control builtins.

`xd-shell` provides a job control system when running interactively in a terminal,
enabling users to run jobs in the foreground or background and to suspend or
resume them as needed.

---

### 7.1 Job States <a name="job-states"></a>

`xd-shell` tracks the state of each active job so it can report changes and
manage jobs correctly during execution.

A job may be in one of the following states:

- **Running** — the job is currently executing.
- **Stopped** — the job has been suspended.
- **Done** — the job finished with an exit status of 0.
- **Exited** — the job finished with a non-zero exit status.
- **Signaled** — the job was terminated by a signal.

---

### 7.2 Foreground and Background Jobs <a name="foreground-and-background-jobs"></a>

When a job is executed in `xd-shell`, it normally runs in the foreground.
Foreground jobs take control of the terminal, receive keyboard input, and the
shell waits for them to finish before reading the next command.

A job can also be started in the background by appending `&` to the command
line. Background jobs do not take control of the terminal, and the shell returns
to the prompt immediately, allowing additional commands to be entered while the
job continues running.

When running interactively, `xd-shell` manages ownership of the terminal by
assigning it to the process group of the job running in the foreground. Terminal
control is returned to the shell whenever that job stops or completes. 
Terminal-generated signals such as `Ctrl+C` (SIGINT) and `Ctrl+Z`
(SIGTSTP) are delivered to the job that currently owns the terminal. Background
jobs do not receive these signals, if they attempt to read from or write to the
terminal, they are stopped with SIGTTIN or SIGTTOU.

To support this behavior and avoid being interrupted or suspended itself, the
shell ignores several signals in interactive mode, including SIGINT, SIGQUIT,
SIGTSTP, SIGTTIN, and SIGTTOU. These signals are intended for the foreground
job’s process group rather than for `xd-shell` itself.

---

### 7.3 Job Specifiers <a name="job-specifiers"></a>

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

The `jobs` builtin does not accept any non-option arguments. It always prints the
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
is omitted, the shell's *current job* (as described in [Job Specifiers](#job-specifiers)) is used. The specified job is brought into the foreground, if it 
was stopped, it is resumed and run in the foreground while `xd-shell` waits for it 
to finish or stop again.

**Exit status:**

Returns the exit status of the command placed in foreground unless an error occurs.

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
the shell's *current job* (as described in [Job Specifiers](#job-specifiers)) is 
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
