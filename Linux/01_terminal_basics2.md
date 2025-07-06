# BASH

(Notes from linuxjourney.com)

The shell program bash (Bourne Again shell): almost all Linux distributions will default to the bash shell. There are other shells available such as ksh, zsh, tsch

Depending on the distribution the shell prompt might change, but for the most part it should adhere to the following format:

```bash
username@hostname:current_directory

pete@icebox:/home/pete $
```

## 1. simple commands

The `echo` command just prints out the text arguments to the display.

```bash
$ echo Hello World
```

Some other commands:

```bash
$ whoami
$ date
```

## 2. pwd (Print Working Directory)
Everything in Linux is a file. Every file is organized in a hierarchical directory tree. The first directory in the filesystem is aptly named the `root` directory. The root directory has many folders and files which you can store more folders and files, etc. Here is an example of what the directory tree looks like:


```
/
|— bin
|   |— file1
|   |— file2
|— etc
|   |— file3
|   `— directory1
|       |— file4
|       `— file5
|— home
|— var
```


The location of these files and directories are referred to as `paths`. If a folder named `home` with a folder inside of it named `pete` and another folder in that folder called `Movies`, that path would look like this: `/home/pete/Movies`.

Navigation of the filesystem, much like real life is helpful if you know where you are and where you are going. To see where you are, you can use the `pwd` command, this command means “print working directory” and it just shows you which directory you are in, note the path stems from the root directory.

```bash
$ pwd
```

## 3. cd (Change Directory)
Now that you know where you are, let’s see if we can move around the filesystem a bit. Remember we’ll need to navigate our way using paths. There are two different ways to specify a path, with absolute and relative paths.

**Absolute path**: This is the path from the root directory. The root is the head honcho. The root directory is commonly shown as a slash. Every time your path starts with `/` it means you are starting from the **root directory**. For example, `/home/pete/Desktop`.

**Relative path**: This is the path from where you are currently in filesystem. If I was in location `/home/pete/Documents` and wanted to get to a directory inside Documents called taxes, I don’t have to specify the whole path from root like `/home/pete/Documents/taxes`, I can just go to `taxes/` instead.

Now that you know how paths work, we just need something to help us change to the directory we want to. Luckily, we have `cd` or “change directory” to do that.

```bash
$ cd /home/pete/Pictures
```

So now I’ve changed my directory location to `/home/pete/Pictures`.

Now from this directory I have a folder inside called `Hawaii`, I can navigate to that folder with:

```bash
$ cd Hawaii
```

Notice how I just used the name of the folder? It’s because I was already in `/home/pete/Pictures`.

It can get pretty tiring navigating with absolute and relative paths all the time, luckily there are some shortcuts to help you out.


* `.` (current directory). This is the directory you are currently in.
* `..` (parent directory). Takes you to the directory above your current.
* `~` (home directory). This directory defaults to your “home directory”. Such as /home/pete.
* `-` (previous directory). This will take you to the previous directory you were just at.

```bash
$ cd .
$ cd ..
$ cd ~
$ cd -
```







