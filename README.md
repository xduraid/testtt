# ⌨️ XD-Readline

Comprehensive, dependency-free command-line editor written in C.  
Inspired by the GNU Readline library, but built from scratch. Ideal for building shells, interactive interpreters, and REPLs.

---

## ❔ Why XD-Readline?

While building [xd-shell](https://github.com/xduraid/xd-shell), I needed readline features like interactive editing, command history, and tab-completion.  
Instead of relying on an external library like GNU Readline, I wrote `xd-readline` from scratch to deepen my understanding of terminal I/O and low-level input processing on Unix-like systems.

---

## 🌟 Features

* Raw-mode terminal I/O for low-level control.
* Manual line wrapping and cursor tracking using ANSI escape sequences.
* Terminal resize handling via `SIGWINCH` signal detection.
* Full input editing with advanced cursor movement (character and word level).
* Built-in history management: add, clear, retrieve, save, and load from file.
* Interactive history navigation.
* Forward and reverse history search with match highlighting.
* Customizable tab-completion via user-defined generator function.
* Lists all possible completions when pressing `Tab` twice.
* Configurable input prompt with support for ANSI SGR color codes.

---

## 📑 Table of Contents

1. [🖋️ Line Editing and Cursor Movement](#line-editing)
2. [💾 History Management](#history-management)
3. [🧭 History Navigation](#history-navigation)
4. [🔍 History Search](#history-search)
5. [🪄 Tab Completion](#tab-completion)
6. [🎨 Prompt Customization](#prompt-customization)
7. [🚀 Integration](#integration)
8. [📝 Notes](#notes)
9. [🤝 Contributing](#contributing)
10. [📜 License](#license)
11. [🔗 Related Projects](#related-projects)

---

## 🖋️ Line Editing and Cursor Movement <a name="line-editing"></a>

`xd-readline` supports full editing of the input line at any cursor position.
You can move the cursor by character or word, jump to the beginning or end of the line, and delete characters or entire words efficiently.

**Key Bindings:**

| Key Combination          | Action                                               |
| ------------------------ | --------------------------------------------------   |
| `←` / `Ctrl+B`           | Move cursor one character to the left                |
| `→` / `Ctrl+F`           | Move cursor one character to the right               |
| `Ctrl+←` / `Alt+B`       | Move cursor one word to the left                     |
| `Ctrl+→` / `Alt+F`       | Move cursor one word to the right                    |
| `Home` / `Ctrl+A`        | Move cursor to the beginning of the line             |
| `End` / `Ctrl+E`         | Move cursor to the end of the line                   |
| `Backspace` / `Ctrl+H`   | Delete character before the cursor                   |
| `Delete`                 | Delete character at the cursor                       |
| `Ctrl+D`                 | Delete character at the cursor, or send EOF if empty |
| `Alt+Backspace`          | Delete word before the cursor                        |
| `Ctrl+Delete` / `Alt+D`  | Delete word after the cursor                         |
| `Ctrl+U`                 | Delete everything before the cursor                  |
| `Ctrl+K`                 | Delete everything from the cursor to the end         |
| `Ctrl+L`                 | Clear the screen                                     |

---

## 💾 History Management <a name="history-management"></a>

`xd-readline` provides built-in history support to store, browse, and manage previously entered lines through a set of utility functions:  

* `xd_readline_history_add(const char *str)`  
  Adds a new entry to the history buffer.

* `xd_readline_history_get(int n)`  
  Retrieves the *n-th* history entry.  
  - Positive values count from the beginning (e.g., `1` = oldest entry).  
  - Negative values count from the end (e.g., `-1` = most recent entry).

* `xd_readline_history_clear()`  
  Clears all stored history entries.

* `xd_readline_history_print()`  
  Prints all history entries to `stdout`.

* `xd_readline_history_save_to_file(const char *path, int append)`  
  Saves the current history to a file.  
  - If `append` is `1`, entries are added to the end of the file.  
  - If `0`, the file is overwritten.

* `xd_readline_history_load_from_file(const char *path)`  
  Loads history entries from a file into the current session.

> ℹ️ **Note:**  
> History entries are stored in a circular array with a fixed maximum capacity defined by the `XD_RL_HISTORY_MAX` macro.  
> By default, this limit is set to `1000`, and you can increase it by changing the macro's value in [xd_readline.h](./include/xd_readline.h).

---

## 🧭 History Navigation <a name="history-navigation"></a>

`xd-readline` supports navigating through previously entered lines using keyboard shortcuts.  
You can step backward and forward through the history, or jump directly to the oldest or most recent entry.

**Key Bindings:**

| Key Combination             | Action                                       |
| --------------------------- | -------------------------------------------- |
| `↑` / `Page Up`             | Navigate to the previous history entry       |
| `↓` / `Page Down`           | Navigate to the next history entry           |
| `Ctrl+↑` / `Ctrl+Page Up`   | Jump to the first (oldest) history entry     |
| `Ctrl+↓` / `Ctrl+Page Down` | Jump to the last (most recent) history entry |

---

## 🔍 History Search <a name="history-search"></a>

`xd-readline` provides real-time, incremental search through history.  
You can search backward or forward, with matches updated live as you type.  
The matched entry is highlighted and can be accepted, skipped, or canceled.

**Key Bindings:**

| Key Combination | Action                                                               |
| --------------- | -------------------------------------------------------------------- |
| `Ctrl+R`        | Start reverse (backward) history search or find next reverse match   |
| `Ctrl+S`        | Start forward history search or find next forward match              |
| `Ctrl+G`        | Cancel the search and restore original input line                    |
| `Esc Esc`       | Accept the current match and exit search mode                        |

## 🪄 Tab Completion <a name="tab-completion"></a>

`xd-readline` supports tab completion via a user-defined function.  
When the user presses `Tab`, this function is invoked to generate an array of possible completions for the text being typed.  
Pressing `Tab` again will display all possible completions.

To enable tab-completion, you must assign your completions generator function to the global completions generator function pointer:

```c
xd_readline_completions_generator = your_completions_generator;
```

The completions generator function must match the following signature:  

```c
char **your_completions_generator(const char *line, int start, int end);
```
Where:
- `line` - The line being read.
- `start` - The start index of the partial text to be completed within the line.  
- `end` - The end index (exclusive) of the partial text to be completed within the line.  

The generator must return a **dynamically allocated**, **NULL-terminated**, and **sorted** array of possible completions. `xd-readline` will automatically free the array and each string after use.

The `start` position is determined by scanning backward from the cursor until a delimiter character is found. These delimiters are defined by the `XD_RL_TAB_COMP_DELIMITERS` macro:

```c
#define XD_RL_TAB_COMP_DELIMITERS "'\"`!*?[]{}()<>~#$`:=;&|@\\%^ "
```

You can customize this set of characters by modifying the macro in [xd_readline.h](./include/xd_readline.h).

> ℹ️ **Note:** A working example of path completion is included in [main.c](./src/main.c).


## 🎨 Prompt Customization<a name="prompt-customization"></a>

The input prompt in `xd-readline` is configured by assigning a string to the global variable:

```c
xd_readline_prompt = "your-prompt> ";
```

The prompt supports ANSI SGR escape sequences for styling (such as color, bold, or underline). These sequences are automatically accounted for during rendering and will not interfere with input positioning or cursor movement.

```C
xd_readline_prompt = "\033[1;32myour-prompt>\033[0m ";
```

> ℹ️ **Note**: 
> The prompt string is not duplicated internally, you should ensure it remains valid while `xd_readline()` is running.

---

## 🚀 Integration <a name="integration"></a>

This library has no dependencies,  just copy `xd_readline.c` and `xd_readline.h` into your project.  
Include the header in your source file, and you're good to go.

A full working example is provided in [main.c](./src/main.c).

---

## 🧾 Notes<a name="notes"></a>

* The library is designed to work only with **terminal I/O**, both `stdin` and `stdout` must be attached to a terminal.  
If either is not a terminal, an error message is printed and the program will be terminated with an error exit code.

- While large input lines are supported, some cursor movement and editing functionalities may not work correctly when the line exceeds the visible screen area (`width × height` characters).  
This is due to the limitations of ANSI escape sequences and the goal of maintaining wide compatibility.  

- The library was developed on **Debian Linux**, using the `xterm-256color` terminal type, and has been tested on different distributions and terminal emulators.

---

## 🤝 Contributing <a name="contributing"></a>

Suggestions, improvements, and bug reports are welcome!  
Feel free to open an issue or submit a pull request.

---

## 📜 License <a name="license"></a>

This project is released under the MIT License. See [LICENSE](./LICENSE) for details.

---

## 🔗 Related Projects <a name="related-projects"></a>

Check out [xd-shell](https://github.com/xduraid/xd-shell), a custom shell built using this library.
