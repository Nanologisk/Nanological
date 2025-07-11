# 04: Shell Scripting Basics

## What is a shell script?
A shell script is a text file with a list of terminal command that run top to bottom. It is saved as .sh and then run like a program

## Make a script

### Step 1. make a file
cd ~/Documents/linux/Linux_Neovim_Journal/scripts
nvim hello.sh

### Step 2. add text inside hello.sh
```
#!/bin/bash

echo "Hello, $USER!"
echo "Today is: $(date)"
echo "You are in: $(pwd)"
```

This script does
- Greets the username
- Shows the current date
- Prints the current folder

Some explanations:
- #!/bin/bash: Use Bash shell to run the script 
	* this is called a shebang (or hashbang). 
	* it tells the system which program should run this script
	* /bin/bash means: run this script using the bash shell
- /bin/bash:
	* /bin/bash is the full path to the Bash shell executable on the system
	* Bash stands for Bourne Again SHell
	* It's a command interpreter, a program that reads commands, scripts and execute them.
	* When opening a terminal in linux distro, we are usually running Bash
	* #!/bin/bash at the top of a script tells the system to use this exact program, (/bin/bashe), to run the sript.
- #!: Shebang
	* #! is called shebang
	* The path following it tells the kernel which interpreter to use.
	* When run the script, the kernel looks at the shebang and launches the interpreter with the script as input.
- On some system, bash might in a different location
	* a more felxible shebang is: ```#!/usr/bin/env bash```
	* #!/usr/bin/env bash: this uses the env program to find Bash in the current environment's PATH dynamically. 
	* It's a common portable way to write scripts.
- echo: print text
	* echo is a command that prints text to the terminal
	* running echo "Hello, world!" prints Hello world!
- USER (all caps): value of enviornment variable USER
	* USER is an enviornment variable, the variable the shell keeps.
	* It holds username
	* writing $USER accesses its value
	* The $ means "give me the value of this variable"
	* echo "Hello, $USER" gives Hello, nan
- $ and $(...): Run command inside and substitute output
	* $ before a variable means "Give me the value of this variable"
	* $(...) means: "Run the command inside and substitute its output there".
	* echo "Today is $(date)" ->  $(date) runs it inside the echo command -> print date
- chmod +x: make file executable
	* chmod means change mode, it changes file permissions
	* +x means add executable permission
	* this let the script be run like a program



### step 3. make it executable

In the terminal:
``` 
chmod +x hello.sh
```

This gives the file permission to run like a program.

### Step 4. run it

```
./hello.sh
```
Here it has to be ./ before hello.sh, because the current directory (.) is not in the system PATH by default, for security reasons.
- When running just `hello.sh` instead of `./hello.sh` 
	* the shell searches for it only incertain system directories, like /usr/bin, or /bin
	* The current directory (.) is usually not included in that search path
	* `./hello.sh` explicitly tells the shell: "Run hello.sh in this current directory (.)."
- if want to run hello.sh from any folder without typer the full path or `./`:
	* move it to a directory that is already in the PATH, like ~/bin
	* f. ex. (do this later, not now)
	* mkdir -p ~/bin
	* mv ~/Documents/linux/Linux_Neovim_Journal/scripts/hello.sh ~/bin/
	* chmod +x ~/bin/hello.sh
	* For this to work, make sure ~/bin is in the PATH (do this later). 

## Commands learned
- chmod +x
- ./scriptname.sh
- echo
- $(...) for command substitution

## Practice
- Created hello.sh in sandbox
- Wrote a script that greets me
- Made it executable with chmod
- Ran it with ./hello.sh

## Notes / Questions
- Why do I need `./` to run a script?
- What does `#!/bin/bash` mean?
- Can scripts take input from user?

