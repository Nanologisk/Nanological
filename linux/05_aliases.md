# 05: Custom Aliases

Goal: learn how to create own command shortcut using alias, and make them persist by adding them to the `.bashrc`.

## How to do it

### 1. make a few aliases to try

From the terminal, run:


```
# Custom Aliases:
alias l='ls -CF'
alias ll='ls -la'
alias ..='cd ..'
alias gs='git status'

# Now using them
# type into terminal:
l
ll
..
gs # this may give error if not in a git folder.

```

Explanations:
- alias l='ls -CF'
  * l is a nwe shortcut to run ls -CF
  * ls lists files and folders
  * -C makes it columnar, -F adds / after folders, * after executables, etc.
- alias ll='ls -la'
  * ll shows a detailed list (-l) including hidden files (-a)
- alias ..='cd ..'
  * .. becomes a shortcut to move up one folder
- alias gs='git status'
  * gs will now run git status (only useful inside a git folder)
- Be very careful: Do not have sapces between the alias name, the =, and the quted command.
  * Because Bash see l, = and 'ls -CF' as separate commands. it doesn't know what to do with that.


### 2. Add them to .bashrc

1. open .bashrc in Neovim:

```
nvim ~/.bashrc
``` 

2. scroll to the bottom and paste the alias definitions:

```
alias l='ls -CF'
alias ll='ls -la'
alias ..='cd ..'
alias gs='git status'
```

3. save and quit (:wq)

4. reload .bashrc:

```
source ~/.bashrc


### 3. create some more!

```
alias dt='date "+%Y-%m-%d %H:%M:%S"'
alias c='clear'
alias d='cd ~/Documents'
alis learning='cd ~/Documents/linux/linux_neovim_basics'
```

Here, 

alias dt='date "+Y-%m-%d %H:%M:%S"'
- dt will now run the date command with a custom format
- date "+%Y-%m-%d %H:%M:%S" prints the current date and time like this:
2025-06-13 21:47:18

Symbol	Meaning	Example
%Y	4-digit year	2025
%m	2-digit month	06
%d	2-digit day	13
%H	Hour (24-hour)	21
%M	Minutes	47
%S	Seconds	18

It is useful becuase it's handy for quickly checking theexact timestamp, and useful when writing logs, notes, or tracking when something happend. It could be made a note-taking alias like:

```
alias logtime='echo $(date "+%Y-%m-%d %H:%M:%S") >> log.txt'
```

Part	Meaning
alias logtime=...	Creates an alias named logtime
echo	Prints text to the terminal (or to a file here)
$(...)	Runs the command inside it and substitutes the result
date "+%Y-%m-%d %H:%M:%S"	Gives a formatted date/time string
>> log.txt	Appends the result to a file called log.txt


## a safe way doing this

### 1. making a backup copy
cp ~/.bashrc ~/.bashrc.backup
This creates a file named .bashrc.backup in the home folder, if anything goes wroong, we can restore it .

### 2. Edit .bashrc
nvim ~/.bashrc
make the changes and save and exit (:wq)

### 3. reload the .bashrc
source ~/.bashrc

### 4. if anything breaks, resotre backup
cp ~/.bashrc.backup ~/bashrc
source ~/.bashrc

this replaces the current .bashrc with the backup and reload it

### 5. version the .bashrc with git
If wanting to be extra safe, the .bashrc can be put under version control with Git.




## Summary:
### Aliases I created
- `ll` → `ls -la`
- `..` → `cd ..`
- `dt` → show current date & time
- `sandbox` → jump to sandbox folder

### New commands learned
- alias
- source ~/.bashrc
- edited ~/.bashrc with Neovim

### Notes / Questions
- What's the difference between ~/.bashrc and ~/.profile?
- Can I organize aliases in a separate file?
- How do I remove or override an alias?

