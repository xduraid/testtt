
### 10.1 Default Environment <a name="default-environment"></a>

During startup, `xd-shell` ensures that a basic set of environment variables is
available. These variables provide information commonly expected by shell users
and external programs.

If a variable is already defined, `xd-shell` leaves it unchanged. Otherwise, it
assigns a default value.

| Variable   | Description                                                                | Default value (if unset)                                  |
|------------|----------------------------------------------------------------------------|----------------------------------------------------------|
| `HOME`     | The user's home directory                                                  | The current user's home directory                        |
| `USER`     | The current user's name                                                    | The current user's login name                            |
| `LOGNAME`  | The user's login name                                                      | The current user's login name                            |
| `PATH`     | Directories searched for external commands                                 | `/usr/local/bin:/usr/local/sbin:`<br>`/usr/bin:/usr/sbin:/bin:/sbin` |
| `SHLVL`    | Shell nesting level                                                        | `1` if login shell,<br> otherwise incremented by `1`       |
| `SHELL`    | Path of the running `xd-shell` executable                                  | Full path to the `xd-shell` executable                   |
| `HISTFILE` | Persistent command history file (interactive shells only)                 | `$HOME/.xdsh_history` or `.xdsh_history`                 |
