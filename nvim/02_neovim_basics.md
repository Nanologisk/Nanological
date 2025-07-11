# 02: Neovim Basics

## Commands learned
- i
- Esc
- :w, :q, :wq, :q!
- dd, yy, p, u, /search

## Four modes:
- normal: Esc -> moving around, rund commands
- insert: i -> type text
- command: : -> run save, quit, etc.
- visual: v -> select text

## Saving and quitting
- save file: :W
- quit file: :q
- save and quit: :wq
- force quit: :q!

## Ohter movement and edit:
dd: delete a whole line
yy: yank(copy) a line
p: paste below
P: paste above
u: undo
/text: search for "text"
n: go to next match in search
N: go to previous match in sesarch
G: go to bottom of file
gg: go to top of file



## Practice
- Typed and saved my second note!
- Deleted and copied lines
- Practiced quitting calmly 😄

## Notes / Questions
- Why do modes exist in Vim?
- How do I search and replace text?

## Code used:

```
cd ~/Documents/linux/Linux_Neovim_Journal
nvim 02_neovim_basics.md

# Type this after pressing "i"
# Neovim is fun! 
This is my second learning.

# Press Esc to return to normal mode

# quite 
:wq 


```



