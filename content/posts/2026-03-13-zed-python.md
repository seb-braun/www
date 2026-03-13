---
title: "Zed as a Python IDE"
description: Out of the box powerful Python IDE
date: 2026-03-13T13:38:01Z
tags: ["zed", "python"]
---

A while back, I [shared](/posts/2025-02-12-zed/) how to use this sleek new editor as a Go IDE. Now I'd like to revisit this topic, this time for [Python](https://www.python.org/).

If you're curious about other languages [Zed](https://zed.dev/) supports, their docs feature an [extensive list](https://zed.dev/docs/configuring-languages).

Today it's all about Python—and how Zed can replace [VS Code](https://code.visualstudio.com/), [PyCharm](https://www.jetbrains.com/fr-fr/pycharm/), or any other Python IDE.

But why choose Zed as your main editor? Why Change?

- It's **Lightning fast**—Rust + GPU UI.  
- It's **Lightweight** vs heavy IDEs.  
- **Collaboration** is built-in.

Scene set—let's dive in!
# Zed
Installing Zed is as simple as
```shell
curl -f https://zed.dev/install.sh | sh
```
In my case, I prefer sticking to the Arch Linux [package](https://archlinux.org/packages/extra/x86_64/zed/) repositories.
```shell
sudo pacman -S zed
```
# Python
Let's assume you already have Python installed. I'm a huge fan of all Arch Linux derivatives—recently switching from [Manjaro](https://manjaro.org/) to [CachyOS](https://cachyos.org/) (topic for another article).

On these systems, if Python's missing, just run:

`sudo pacman -S python`

There's even a newer, recommended way to install Python that makes virtual environments dead simple.

```shell
sudo pacman -S uv
uv python install 3.14
uv venv
```

Quick check

```shell
python --version
```
# IDE
By default, Zed provides **most Python features out-of-the-box** when opening a project.

- Syntax highlighting
- Language Server support (LSP) with [basedpyright](https://github.com/DetachHead/basedpyright)
- Error diagnostics
- Code navigation
- Outline view / symbol index
- Import auto-completion
- Snippets
- Code Formatting with [Black](https://black.readthedocs.io/en/stable/)
- Code Linting with [Ruff](https://docs.astral.sh/ruff/)
# Requirements
In this section, we'll install key dependencies to unlock Zed's full Python potential.
```shell
sudo pacman -S ruff python-black
```
Again an alternative would be
```
uv tool install ruff black
```
For [basedpyright](https://github.com/DetachHead/basedpyright) (faster fork of [pyright](https://github.com/microsoft/pyright), full-featured static type checker that complements Ruff), no official package → AUR or uv. We'll use:
```shell
uv tool install basedpyright
uv tool list
basedpyright v1.38.2
- basedpyright
- basedpyright-langserver
```
Now let's tie it all together with a few config tweaks.
# Test project
First, let's create a test project.
```shell
mkdir dp
cd dp
uv init
uv venv
source .venv/bin/activate
```
Open your test project in Zed now.
```
zed .
```
# Testing
{{< checklist >}}
Open _main.py_ and you'll immediately see **syntax highlighting** in action.
Add a few lines of Python code to your project and watch your **Language Server** basedpyright kick in out-of-the-box
to illustrate **error diagnostic**, remove the semi column after _main_ and observe red appearing
`Ctrl+Click` the last _main()_ call to jump to its definition—**code navigation** rocks! You can also navigate through symbols using Ctrl+Shift+O.
Hover over any symbol → Zed **shows type information**, docstrings, and inferred types.
_Select a symbol → Right-click → Find References_, Zed **lists every location** in the project where it is used.
use `Ctrl+T` for **Workspace-wide Symbol Search**
Now add _import math_, then on next line type _math._ —watch **autocomplete** instantly show available functions! Use `Tab` to accept a suggestion.
{{< /checklist >}}

# Code Formatting
Now add the following function to your _main.py_
```python
def greet(name):return f"Hello {name}"  
x= 5  
y =10
```
Hit `Ctrl+Shift+I` to auto-format your code.
**Format on Save** is enabled by default—formatting happens automatically on save.

You'll notice these changes:
- Proper indentation
- Spaces around operators
- Function body on new line
- Consistent style throughout
# Code Linting
Create a new _bad.py_ file in your project with this content for a deeper example:
```python
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
Scan for all the warnings—Ruff's done its job.
Save to see auto-formatting in action.
# Snippets
Python snippets turbocharge your Zed workflow—super easy setup.

**Ctrl+Shift+P** → configure snippets → **Python** → JSON file opens.

A _Try / Except_ snippet example
```JSON
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
- **prefix**: Typed text + `TAB` → snippet trigger
- **body**: Code inserted
- **$0**: Final cursor position after all TAB navigations.

Another example

```JSON
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
```

Add the second snippet right after the first in the JSON block, comma-separated.
# Ctrl+Shift+P: Instant Python Execution

Lastly we'll create a task and assign a hotkey to easily run Python code without leaving the editor.

**Ctrl+Shift+P** → open tasks → JSON file opens.

As we're using `uv` this is what we need to insert in the task block

```JSON
{
  "label": "Run Python file",
  "command": "uv",
  "args": ["run", "$ZED_FILE"],
  "cwd": "$ZED_WORKTREE_ROOT"
}
```

Now to assign this task to a hotkey

**Ctrl+Shift+P** → open keymap file → JSON file opens.

this is what you need to add

```JSON
{
  "context": "Workspace",
  "bindings": {
    "ctrl-shift-r": ["task::Spawn", { "task_name": "Run Python file" }]
    }
}
```

Now by pressing `Ctrl+Shift+R` Your code runs—output appears in Zed's console.
# Wrap-up
Compared to my [Neovim](/posts/2025-01-30-neovim/) article, we achieve tons with near-zero config.

Zed = Neovim power + VS Code UX, without Electron.

That's Zed's magic—simple yet powerful. Enjoy!

# Links
- [How to Set Up Python in Zed](https://zed.dev/docs/languages/python#how-to-set-up-python-in-zed)
