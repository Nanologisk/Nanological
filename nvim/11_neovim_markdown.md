
# Enable syntax highlighing and line wrapping

---
## Configuration settings for Neovim

First check
```
nvim --version | grep "Compiled"
```

Or indie Noevim:

```
# From terminal:
Neovim

# inside NEovim:
:echo stdpath("config")

# it shows /home/nan/.config/nvim
```

Now create configuration file:

- create the directory manually: this created the `nvim' folder inside `~/.config'.
``` 
mkdir -p ~/.config/nvim
```
-Create the config file
```
nvim ~/.config/nvim/init.vim
```
- add the following to ~/.vimrc (Vim) or ~/.config/nvim/init.vim (Neovim):

```
" Neovim basic config

syntax on
filetype plugin indent on
set wrap
set linebreak
set number
set tabstop=4
set shiftwidth=4
set expandtab
set autoindent
" set cursorline # this highlight the entire line, so removed it

```

### Live preview in browser

 - install grip from terminal window (can run python3 --version to check availabel version)
 - Install grip (render markdwon to live preview server)

```
pip3 install grip
```

 - in terminal, run

```
grip my-notes.md
```

This will start a local server (e.g. http://localhost:6419) where the rendered markdown can be viewed in the browser.
Every save (:w) in Neovim will auto-refresh the preview.


### example

```
nvim my-doc.md
:w # save
:q # quit
```
in parellell terminal tab:
`grip my-doc.md`



---


## Install markdown preview plugin

### **1. Locate Noevim config folder**
Previously a config.folder was created by `mkdir -p ~/.config/nvim`

### **2. Create or change the basic config file**
```
# Open the file for edicting
nvim ~/.config/nvim/init.vim

# start editing
" Neovim basic config
 
syntax on
filetype plugin indent on
set wrap
set linebreak
set number
set tabstop=4
set shiftwidth=4
set expandtab
set autoindent
" set cursorline # this highlight the entire line, so removed it

# This is also something which was created before
```

### **3. Install plugin manager (vim-plug)**
This tool helps add plugins, by running in terminal:

```
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

```
This downloads vim-plug into Neovim folder.

### **4. Add markdown preview in config**

Reopen `init.vim` file:
```
nvim ~/.config/nvim/init.vim
```

Add these lines **at the top** (before `syntax on`)

```vim
call plug#begin('~/.local/share/nvim/plugged')

Plug 'iamcco/markdown-preview.nvim', { 'do': 'cd app && npm install' }

call plug#end()
```

So the entire file look like:

```vim
" Neovim basic config

call plug#begin('~/.local/share/nvim/plugged')

Plug 'iamcco/markdown-preview.nvim', { 'do': 'cd app && npm install' }

call plug#end()

syntax on
filetype plugin indent on
set wrap
set linebreak
set number
set tabstop=4
set shiftwidth=4
set expandtab
set autoindent
" set cursorline

```
Save and exit (:wq)


Here, `call plug#begin('~/.local/share/nvim/plugged')`: 
- this **starts** the section where the declare plugin with `vim-plug`.
- the string `'~/.local/share/nvim/plugged'` is the folder where plugins will be **dowloaded and stored** on the computer.
- It's more like: Hey vim-plug, let's start listing pugins and save them there.

and `Plug 'iamcco/markdown-preview.nvim', { 'do': 'cd app && npm install' }`:
- This line tells vim-plug to **install the plugin** called `markdown-preview.nvim` from GitHub user `iamcco`.
- The part `{ 'do': 'cd app && npm install' }` means:
-- after downloading the plugin, go into its `app` folder and run `npm install` (Node.js package installer).
-- This installs the necessary JavaScript dependencies for the plugin to work properly

Finally, `call plug#end()`:
- This **ends** the plugin declaration block
- It tells vim-plug: i'm down listing plugins, now go and process them (install/update/load).


#### In short:
- `call plug#begin(...)` and `call plug#end()` wrap list of plugins.
- `Plug 'plugin-name'` inside that block adds a plugin to be managed.
- The `{ 'do': '...' }` part runs extra setup commands after installing


### **5. Install Node.js**

```bash
sudo apt install nodejs npm
```
- Node.js is a program that run JavaScript code outside of a web browser, on the computer.
- npm stands for **Node Package Manager**. It’s a tool that comes with Node.js and helps to install and manage JavaScript packages (libraries, tools, plugins, etc.).


### 6. Install the plugin inside Neovim
```bash
nvim
```

Then run
```vim
:PlugInstall
```

### 7. Use markdown preview

```bash
nvim README.md
```

Inside NEovim, type

```vim
:MarkdownPreview
```








This tells Markdown “this code block is fenced with 4 backticks, so inside I can put 3 backticks literally.”



