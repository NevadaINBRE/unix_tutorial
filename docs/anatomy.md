## 1. File and Folder Organization

Similar to Windows computers, UNIX has a directory structure to organize data.

![](https://www.geeksforgeeks.org/wp-content/uploads/unix.png) 

At the very top of your system and this image, there is the root directory `/`. This is analogous to the `C:\` drive in Windows.

Below that, there are many "system" folders which the UNIX operating system uses to run (`bin`, `dev`, `etc`, `lib`, `proc`, `sbin`, etc). The `home` folder contains user directories similar to the "Users" folder in windows and mac systems.

Let's login and open the terminal to start learning some commands and practice navigating the file/folders.

## 2. Anatomy of a Linux Command

Linux follows rules for command syntax. Let's take a look at the `cp` command example below.

```bash
cp -R myFolder ~/Somewherelse/tocopy
```

**Understanding the Command:**

* **`cp`**: The first part of the command is the program/utility you are telling Linux to run. These programs need to be accessible via the `$PATH` environmental variable.
* **`-R`**: The second (and optional) part of any command is the command line flag(s). Flags allow users to augment the default behavior of the command in some way. In this case, recursively copy files/folders. Flags are also sometimes called parameters.
* **`myFolder ~/Somewherelse/tocopy`**: The third part of any command is the command line argument(s). In this case, we have two arguments instructing the `cp` command to copy the data in `myFolder` to a location `~/Somewherelse/tocopy`.

## 3. First Linux/UNIX Commands

On a Linux system, the default directory when logging into the system will be your user's home folder. This is signified by the tilde `~` character.

To find out the full path of where you are located on the operating system, use the `pwd` command.

```bash
pwd
```

Let's make a folder called `training` for this workshop using the `mkdir` command.

```bash
mkdir training
```

Use the `ls` command to verify if the training folder has been made.

```bash
ls
```

## 4. What is a Shell?

A Unix shell is a command-line interpreter or shell that provides a command line user interface for Unix-like operating systems. The shell is both an interactive command language and a scripting language, and is used by the operating system to control the execution of the system using shell scripts.

So the commands we were running above were sent to the Linux operating system by a shell. The default shell on Pronghorn is BASH. For today, we will be issuing commands interactively. However, in the next meeting, we will create "bash scripts" in order to automate our analyses (and get them to run on Pronghorn as scheduled jobs).
