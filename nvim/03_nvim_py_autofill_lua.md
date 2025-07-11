# Set up Python for Neovim

The python environment is in `~/python_envs/ml_models`. This path is already created.

## Basic set-up

### 1. Activate the enviroument

In terminal:
 ```bash
 source ~/python_envs/ml_models/bin/activate
 ```

 and this changes the prompt to
 ```bash
 (ml_models) user@system:~@
 ```

### 2. install packages as usual in termianl
 
```
pip install numpy pandas
```

### 3. make a test py
```bash
touch test.py
vim test.py
```

change the test.py to run
```python
import pandas as pd
df = pd.Dataframe({
   'name' = [1, 2, 3],
   'test' = [4, 5 ,6]
})

print(df.head())
```

### 4. save and run from terminal

Two ways to run the file. Either call the **python interpreter** (`python3`) or run it as an **executable program**.

#### Call the interpreter:
- Explicitly calling the python interpreter
- passing the script to run test.py
- the script file doesnt need execute permissions
- the **shebang line** (`#!/usr/bin/env python`) is ignored

```bash
python3 test.py
```

#### Run like an executable program
Running with `./test.py` just like a binary.

This only works if:
- The file has a **shebang line** at the top, like
```python
#!/usr/bin/env python3
```
This tells linux: Use what ever `python` is first in the current environemnt's PATH.
So if virtualenv is activated, it will us that python. That is more reliable than hardcoding `python3` or `/usr/bin/python`.

- The script is marked as executable
```bash
chmod +x test.py
```

- The system reads the shebang to know **which interpreter** to use.

This can be used when
- the script is used like a command
- reply on shebang to choose the right python interpreter (e.g. the one from virtualenv).


### 5. autocompelet to Neovim: `coc`

Use `coc.nvim`.

#### Install a plugin manager: `vim-plug`:

```bash
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
  https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

#### Edit Neovim config

Open 
```bash
nvim ~/.config/nvim/init.vim
```

At the top, add
```
call plug#begin('~/.vim/plugged')

" Add coc.nvim plugin
Plug 'neoclide/coc.nvim', {'branch': 'release'}

call plug#end()
```

#### Install the plugin
Open Neovim

```
nvim
```
and run

```vim
:PlugInstall
``` 

Wait until it's done.

#### Install python support in coc.nvim
```vim
CocInstall coc-pyright
``` 

***We can disable and unable autosuggestions by:***
- Turn off Coc.nvim: `CocDisable`
- Turn it back: `CocEnable`


### 6. Auto-pair plugin (`nvim-autopairs`) and smart-skipping using `lua`
autopair: add in the init.vim (in the beginning where install different plugins)


#### Tried the first plugin (installed but later uninstalled)
- Tried jiangmiao/autopairs. First installed:

```vim
" Auto-close bracket and quotes
Plug 'jiangmiao/auto-pairs'

```

Then run:
```vim
:source %
:PlugInstall

```

- Now I would like to use something else, then in init.vim, remove the line

```vim
Plug 'jiangmiao/auto-pairs'
```
Then run `:PlugClean` in Neovim to removed the plugin.
Prompted to confirm removal - type `y`.
Restart nvim

#### More complicated! Using `nvim-autopairs` with coc.nvim

a. Plugin setup:
In `~/.config/nvim-autopairs`, add this line:
```vim
Plug 'windwp/nvim-autopairs'
```
Install the plugin:
```vim 
:source %
PlugInstall 
```

b. Create Lua Config file (first time only)
Run in terminal:
```bash
mkdir -p ~/.config/nvim/lua
nvim ~/.config/vim/lua/autopairs.lua
```

Paste the following inside:
```lua

-- Basic safe config for autopairs
require("nvim-autopairs").setup({
    check_ts = true, -- enable Treesitter support (optional)
    map_cr = true, --auto insert `()` og `{}` on Endter

```
save and exit `wq`

c. Or: if want to **smart skipping**, change the filetext in lua to:
```lua
-- Safe and extendable config for nvim-autopairs

-- Load the plugin
local npairs = require("nvim-autopairs")

-- Set up with some smart defaults
npairs.setup({
  check_ts = true,              -- Enable Treesitter support for better context
  map_cr = true,                -- Automatically insert pairs on Enter
  disable_filetype = { "TelescopePrompt" },  -- Disable in Telescope search
  fast_wrap = {},               -- Enable fast wrapping (optional, but harmless)
})

-- We can add more custom pairing rules below if needed:
-- For example, to add special pairs for a LaTeX filetype:
local Rule = require("nvim-autopairs.rule")
 npairs.add_rules({
   Rule("$$", "$$", "tex")
 })

```

**Smart-skipping**: If already type a `(`, the auto-closing will show `()`. And if typing an extra `)`, it will skip and still show `()`


d. Load the lua file from `init.vim`
Open `~/.config/nvim/init.vim` and add this ***near the bottom***:
```vim
" Load Lua file for autopairs
lua require("autopairs")
```

This tells Neovim to run the lua file we just made.

e. Restart Neovim and reload
```vim
:luafile ~/.config/nvim/lua/autopairs.lua
```

f. For nvim-autopair to work, wee also need to add Treesitter to plugin list
In `init.vim` add
```vim 
Plug 'nvim-treesitter/nvim-treesitter', {'do': ':TSUpdate'}
```
And install it using `:PlugInstall`

Add a new lua file: `nvim ~/.config/nvim/lua/treesitter.lua`, and add
```lua
require'nvim-treesitter.configs'.setup {
  ensure_installed = { "python" },
  highlight = { enable = true },
  indent = { enable = true },
}
```

Back to `init.vim`, add at the end: 
```vim
lua require('treesitter')
```

Reopen nvim. optional: Reload in nvim:
```vim
:luafile ~/.config/nvim/lua/autopairs.lua
```

Option: restart neovim and try `:TSInstall python`. This will install the python parser.


### 7. Adding smart Tab in Coc
Change the line in `~/.config/nvim/init.vim`, using this:

```vim
inoremap <expr> <Tab> pumvisible() ? "\<C-n>" :
      \ matchstr(getline('.'), '\%' . col('.') . 'c.') =~ '[)\]}\"'']' ? "\<Right>" :
      \ "\<Tab>"

```
- This first chekcs if popup is visibale (`pumvisible()`)
-> If yes, send <C-n> (next suggestion)
- Else, cheks the charater at the cursor with `matchstr(getline('.'), '\%' . col('.') . 'c.')`
-> This is a slightly different way to get the charater at cursor position
- If it's a closing char, send `<Right>` to skip.
- Else, insert normal `<Tab>`

To use it:
- <C-n> = `Ctrl + n` : moves down to the next autocomplete suggestion.
- <C-p> = `Ctrl + p` — moves up to the previous autocomplete suggestion.
- <C-e> = `Ctrl + e` — cancels/closes the autocomplete popup without selecting anything.
- <Tab> = `Tab` — typically inserts a tab or jumps over closing brackets (with smart mappings).
- <Right> = `-> arrow` — moves the cursor one character to the right (used for skipping closing brackets).
- <C-h> = `Ctrl + h` — this one is a bit special: it usually acts like Backspace (delete the character before the cursor).


### 8. Attempts to skip signature help but doesn't work yet

####. Created jason file
```bash
mkdir -p ~/.config/nvim 
# The -p makes sure that if the folder exits from before, it would do so much

nvim ~/.config/nvim/coc-settings.json

```
Add the JSON content:
```jason
{
  "python.analysis.completeFunctionParens": false, // Prevents automatically adding parentheses after function names in completions.
  "signature.help.enable": false, //Disables the popup showing function parameter hints/signature while typing.
  "hover.enable": false, //  Disables the popup that shows documentation when you hover over symbols.
  "python.jediEnabled": false,       // disable Jedi if enabled
  "python.analysis.typeCheckingMode": "off"   // disables extra type info
}
```

#### Restart Coc properly
Convirm Coc and python extensions version:
```vim
:CocList extensions
```
or update 
```vim
:CocUpdate
```
Resetart Coc 
```vim
:CocRestart
```


#### disable signature help popup in `init.vim`, adding:

```vim
let g:coc_enable_signature_help = 0
```

#### Things that are not tried yet:

- Override snippet behaviour for function completions. Add to `coc-settings.json`:
```jason
{
  "suggest.snippetIndicator": " ",
  "suggest.snippetSupport": false
}

```
This disables snippet expansion **globally**, so i may lose snippet completions too. so perhap no...

- Switch python language server: use Jedi instead, add in `coc-settings.json`:
```jason
{
  "python.jediEnabled": true
}

```















