# 03: Dotfiles and Configs

Goal: understand what dotfiles are, why they matter and safely edit a simple one.

## What are dotfiles?
In Linux, configuration files usually start with a dot (.) and live in the home directory.
Examples:
- ~/.bashrc - custom commands and terminal settings
- ~/.profile - user environment config
- ~/.gitconfig - git settings
- ~/.vimrc or ~/.config/nvim/init.vim .- vim/Neovim settings

These doesn't show unless we use
ls -a

### codes to try

```
# Go to home directory
cd ~

# see hidden files
ls -a

# safely edit .bashrc
cp ~/.bashrc ~/bashrc.backup
#!!! Always do this before editing configs. it's like a save point

# open it with neovim
nvim ~/.bashrc

# scroll to the bottom and add:
--
# custom alias for easier listing
alias ll='ls -la'

# save and quit
:wq
--

# apply the changes
source ~/.bashrc

# try the new alias:
ll

# it should list files in long format including dotfiles
```
Explanations:
- alias as a shortcut for a longer command
- alias ll='ls -la': this means 
  * everytime typing "ll", it's the same as typing "ls -la"
  * it's just like a nick name
- ls -la:
  * ls: list files
  * -l: long format (shows permissions, sizes, dates, etc.)
  * -a: all files (includes dotfiles like .bashrc)
  * ls -la: lists everything in a folder, with extra details
- source:
  * this is how to reload a config file without closing and reopening the terminal
  * source ~/.bashrc: this tells the terminal to apply the change made to .bashrc now.

### Revert to original version
After trying the configuration, go back to original version

```
# as we already did
cp ~/.bashrc ~/.bashrc.backup

# 1. replace current file with the backup
cp ~/.bachrc.backup ~/.bachrc
# this copies the backup over the edited version

# 2. reload the reverted config
source ~/.bashrc
# now the shell session uses the original .bashrc without alias or other eidts

# 3. clean up the backup
rm ~/.bashrc.backup

```







## Commands learned
- ls -a
- cp ~/.bashrc ~/.bashrc.backup
- nvim ~/.bashrc
- alias
- source ~/.bashrc

## Practice
- Saw hidden files with ls -a
- Backed up and edited .bashrc
- Added my first alias: ll = ls -la

## Notes / Questions
- What’s the difference between .bashrc and .profile?
- Where should I keep personal scripts?

