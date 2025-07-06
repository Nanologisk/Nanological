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

## 4. ls (List Directories)
Now that we know how to move around the system, how do we figure out what is available to us? Right now it’s like we are moving around in the dark. Well, we can use the wonderful `ls` command to list directory contents. The `ls` command will list directories and files in the current directory by default, however you can specify which path you want to list the directories of.

```bash
$ ls
$ ls /home/pete
```

ls is a quite useful tool, it also shows you detailed information about the files and directories you are looking at.

Also note that not all files in a directory will be visible. Filenames that start with `.` are hidden, you can view them however with the `ls` command and pass the `-a` flag to it (a for all).

```bash
$ ls -a
```

There is also one more useful ls flag, `-l` for long, this shows a detailed list of files in a long format. This will show you detailed information, starting from the left: file permissions, number of links, owner name, owner group, file size, timestamp of last modification, and file/directory name.

```bash
$ ls -l
```

```
pete@icebox:~$ ls -l

total 80

drwxr-x— 7 pete penguingroup   4096 Nov 20 16:37 Desktop

drwxr-x— 2 pete penguingroup   4096 Oct 19 10:46  Documents

drwxr-x— 4 pete penguingroup   4096 Nov 20 09:30 Downloads

drwxr-x— 2 pete penguingroup   4096 Oct  7 13:13   Music

drwxr-x— 2 pete penguingroup   4096 Sep 21 14:02 Pictures

drwxr-x— 2 pete penguingroup   4096 Jul 27 12:41   Public

drwxr-x— 2 pete penguingroup   4096 Jul 27 12:41   Templates

drwxr-x— 2 pete penguingroup   4096 Jul 27 12:41   Videos
```

Commands have things called flags (or arguments or options, whatever you want to call it) to add more functionality. See how we added `-a` and `-l`, well you can add them both together with `-la`. The order of the flags determines which order it goes in, most of the time this doesn’t really matter so you can also do `ls -al` and it would still work.

```bash
$ ls -la
```

Some more commands:

* `ls -R`: recursively list directory contents
* `ls -r`: reverse order while sorting
* `ls -t`: sort by modification time, newest first


## 5. touch
Let’s learn how to make some files. A very simple way is to use the touch command. Touch allows you to the create new empty files.

```bash
$ touch mysuperduperfile
And boom, new file!
```

Touch is also used to **change timestamps** on existing files and directories. Give it a try, do an `ls -l` on a file and note the timestamp, then touch that file and it will update the timestamp.

## 6. file: to find the file type
The filename didn’t conform to standard naming like with other operating systems like Windows. Normally you would expect a file called banana.jpeg and expect a JPEG picture file.

In Linux, filenames aren’t required to represent the contents of the file. You can create a file called funny.gif that isn’t actually a GIF.

To find out what kind of file a file is, you can use the file command. It will show you a description of the file’s contents.

```bash
$ file banana.jpg
```

## 7. cat
How to read a file. A simple command to use is the cat command, short for **concatenate**, it not only displays file contents but it can combine multiple files and show you the output of them.

```bash
$ cat dogfile birdfile
```

It’s not great for viewing large files and it’s only meant for short content. There are many other tools that we use to view larger text files.


## 8. less
If you are viewing text files larger than a simple output, less is more. (There is actually a command called `more` that does something similar, so this is ironic.) The text is displayed **in a paged manner**, so you can navigate through a text file page by page.

Once you’re in the less command, you can actually use other keyboard commands to navigate in the file.

```bash
$ less /home/pete/Documents/text1
``` 
Use the following command to navigate through less:

* q - Used to quit out of less and go back to your shell.
* Page up, Page down, Up and Down - Navigate using the arrow keys and page keys. 
* g - Moves to beginning of the text file.
* G - Moves to the end of the text file.
* /search - You can search for specific text inside the text document. Prefacing the words you want to search with /
* h - If you need a little help about how to use less while you’re in less, use help.


## 9. history
In your shell, there is a history of the commands that you previously entered, you can actually look through these commands. This is quite useful when you want to find and run a command you used previously without actually typing it again.

```
$ history
```

Want to run the same command did before, just hit the up arrow.

Want to run the previous command without typing it again? Use `!!`. If you typed `cat file1` and want to run it again, you can actually just go `!!` and it will run the last command you ran.

Another history shortcut is `ctrl-R`, this is the reverse search command, if you hit `ctrl-R` and you start typing parts of the command you want it will show you matches and you can just navigate through them by hitting the `ctrl-R` key again. Once you found the command you want to use again, just hit the `Enter` key.

Our terminal is getting a little cluttered no? Let’s do a little cleanup, use the clear command to clear up your display.


```
$ clear
```

One of the most useful features in any command-line environment is **tab completion**. If you start typing the beginning of a command, file, directory, etc and hit the Tab key, it will autocomplete based on what it finds in the directory you are searching as long as you don’t have any other files that start with those letters. For example if you were trying to run the command chrome, you can type chr and press Tab and it will autocomplete chrome.














