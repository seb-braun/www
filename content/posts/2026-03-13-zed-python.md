---
title: "Zed as a Python IDE"
description: A powerful Python IDE out of the box
date: 2026-03-13T13:38:01Z
tags: ["zed", "python"]
---

A while back, I [shared](/posts/2025-02-12-zed/) how to use this sleek new editor as a Go IDE. Now I'd like to revisit the topic, this time for [Python](https://www.python.org/).

If you're curious about the other languages [Zed](https://zed.dev/) supports, its documentation includes an [extensive list](https://zed.dev/docs/configuring-languages).

Today, it's all about Python and how Zed can replace [VS Code](https://code.visualstudio.com/), [PyCharm](https://www.jetbrains.com/pycharm/), or another Python IDE.

But why choose Zed as your main editor?

- It's **lightning fast**, thanks to Rust and a GPU-accelerated UI.
- It's **lightweight** compared with traditional IDEs.
- **Collaboration** is built in.

Scene set. Let's dive in!

## Zed

Installing Zed is as simple as running:

```shell
curl -f https://zed.dev/install.sh | sh
```

I prefer installing the official Arch Linux [package](https://archlinux.org/packages/extra/x86_64/zed/):

```shell
sudo pacman -S zed
```

## Python

Let's assume you already have Python installed. I'm a huge fan of Arch Linux derivatives and recently switched from [Manjaro](https://manjaro.org/) to [CachyOS](https://cachyos.org/), but that's a topic for another article.

On these systems, if Python is missing, install it with:

```shell
sudo pacman -S python
```

Alternatively, [uv](https://docs.astral.sh/uv/) makes installing Python and creating virtual environments simple:

```shell
sudo pacman -S uv
uv python install 3.14
uv venv
```

Check the active Python version with:

```shell
python --version
```

## IDE features

When you open a Python project, Zed provides **most IDE features out of the box**:

- Syntax highlighting
- Language Server support (LSP) with [basedpyright](https://github.com/DetachHead/basedpyright)
- Error diagnostics
- Code navigation
- Outline view and symbol index
- Import autocompletion
- Snippets
- Code formatting with [Black](https://black.readthedocs.io/en/stable/)
- Code linting with [Ruff](https://docs.astral.sh/ruff/)

## Requirements

Install the key dependencies to unlock Zed's full Python potential:

```shell
sudo pacman -S ruff python-black
```

Alternatively, install them with `uv`:

```shell
uv tool install ruff
uv tool install black
```

[basedpyright](https://github.com/DetachHead/basedpyright) is a faster fork of [Pyright](https://github.com/microsoft/pyright) and a full-featured static type checker that complements Ruff. There is no package in the official Arch repositories, so install it from the AUR or with `uv`. We'll use `uv`:

```shell
uv tool install basedpyright
uv tool list
```

The output should include:

```text
basedpyright v1.38.2
- basedpyright
- basedpyright-langserver
```

Now let's tie everything together with a few configuration tweaks.

## Test project

First, let's create a test project.

```shell
mkdir dp
cd dp
uv init
uv venv
source .venv/bin/activate
```

Now open the project in Zed:

```shell
zed .
```

## Testing the IDE features

{{< checklist >}}
Open `main.py` to see **syntax highlighting** in action.
Add a few lines of Python and watch the **basedpyright language server** start automatically.
To see **error diagnostics**, remove the colon after `main()` in its function definition. Zed immediately highlights the error in red.
`Ctrl+Click` the final `main()` call to jump to its definition. You can also navigate through symbols with `Ctrl+Shift+O`.
Hover over any symbol to see its **type information**, docstring, and inferred type.
Select a symbol, right-click it, and choose **Find References** to list every place where it is used.
Use `Ctrl+T` for **workspace-wide symbol search**.
Add `import math`, then type `math.` on the next line. **Autocompletion** immediately lists the available functions; press `Tab` to accept a suggestion.
{{< /checklist >}}

## Code formatting

Add the following code to `main.py`:

```python
def greet(name):return f"Hello {name}"
x= 5
y =10
```

Hit `Ctrl+Shift+I` to auto-format your code.

**Format on Save** is enabled by default, so formatting also happens automatically whenever you save the file.

You'll notice the following changes:

- Proper indentation
- Spaces around operators
- Function body on new line
- Consistent style throughout

## Code linting

Create a new `bad.py` file with the following content for a more detailed example:

```python
import os  # Unused import (F401)
import sys  # Unused import (F401)


def greet(name):
  print("Hello " + name)


def calculate_total():
  subtotal = 10  # Local variable assigned but never used (F841)
  return 42
import os # Unused import (F401)  
import sys # Unused import (F401)  
  
x=5 # Missing space around operator (E225)  
y =10 # Inconsistent spacing (E225)  
  
def greet(name):  
print("Hello "+name) # String concatenation instead of f-string (WPS305), wrong indentation  
  
def add_numbers(a,b): # Missing whitespace after comma (E231)  
return a+b # Wrong indentation (E111)  
  
def unused_function(): # Function never used (F841)  
pass  
  
greet("Alice")  
result = add_numbers(2,3)  
print(result)  
  
z = 10 # Extra whitespace (E222)
```

Ruff highlights each issue inline. Save the file to see auto-formatting in action as well.

## Snippets

Python snippets can speed up your Zed workflow and are easy to configure.

Press `Ctrl+Shift+P`, select **Configure Snippets**, and then choose **Python** to open the JSON configuration file.

Here is a `try`/`except` snippet:

```json
{
  "Python try except": {
    "prefix": "t",
    "body": [
      "try:",
      "    $1",
      "except ${2:Exception} as e:",
      "    print(e)",
      "    $0"
    ],
    "description": "Python try/except block"
  }
}
```

- **prefix**: the text you type before pressing `Tab` to trigger the snippet
- **body**: the code Zed inserts
- **$0**: the final cursor position after you move through the placeholders with `Tab`

Here is another example that adds a Python main guard:

```json
{
  "Python main guard": {
    "prefix": "m",
    "body": [
      "#!/usr/bin/env python3",  
      "",
      "def main():",
      "    $0",
      "",
      "if __name__ == \"__main__\":",
      "    main()"
    ],
    "description": "Python main entry point"
  }
}
```

To use both snippets, place them inside the same top-level JSON object and separate them with a comma.

## Run Python with a keyboard shortcut

Lastly, we'll create a task and assign a keyboard shortcut so that you can run Python code without leaving the editor.

Press `Ctrl+Shift+P` and select **Open Tasks** to open the JSON configuration file.

Because we're using `uv`, add the following task:

```json
{
  "label": "Run Python file",
  "command": "uv",
  "args": ["run", "$ZED_FILE"],
  "cwd": "$ZED_WORKTREE_ROOT"
}
```

Next, press `Ctrl+Shift+P` and select **Open Keymap File**. Add the following key binding:

```json
{
  "context": "Workspace",
  "bindings": {
    "ctrl-shift-r": ["task::Spawn", { "task_name": "Run Python file" }]
  }
}
```

Press `Ctrl+Shift+R` to run the current file. Its output appears in Zed's terminal panel.

## Wrap-up

Compared with my [Neovim](/posts/2025-01-30-neovim/) article, this setup gives us a lot with almost no configuration.

Zed combines Neovim's power with VS Code's user experience, without Electron.

That's Zed's appeal: simple yet powerful. Enjoy!

## Links

- [How to Set Up Python in Zed](https://zed.dev/docs/languages/python#how-to-set-up-python-in-zed)
