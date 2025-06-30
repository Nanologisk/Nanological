# 01. Terminal Basics

## Commands
- pwd: where am i?
- ls: what's here
- ls -l: list files with long format (show permissions, sizes, dates, etc.)
- ls -a: list all files (includes dotfiles)
- ls -la: list all files with long format 
(see also 03_dotfiles.md)
- cd: move around
- mkdir: make a new folder
- touch: create a file
- rm: remove a file (careful!)

## Practice
- Created a sandbox folder
- Created files: notes.txt, data.csv
- Navigated between folders


## Notes
- How to safely remove files without mistakes?
- What does `..` mean in ``cd ..`` exactly? This means go up one folder!

## Neovim: install and run

```
# install
sudo apt update && sudo apt install neovim

# Run
nvim 01_terminal_basics.md

```
The navigation is very similar to vim

## codes used
```
pwd
# change directory:
cd ~/Documents/linux

# make new folder
mkdir Linux_Neovim_Journey

# make new files
touch 01_terminal_basics.md  03_dotfiles.md   05_aliases.md   07_cron.md
02_neovim_basics.md    04_scripting.md  06_env_vars.md

# have a look
ls

# go up to one folder
cd ..

# make a new file there
touch test.md

# remove that file
rm test.md

# make new folders 
mkdir notes scripts README.md

# delete folder README.md
rmdir README.md

# move .md files to the /notes folder
mv *.md notes/

# create and edit in README.md
nvim README.md

```





