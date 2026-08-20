# Introduction to Unix/Linux

![](https://assets.techrepublic.com/uploads/2021/08/tux-new.jpg) 

Many Research Computing tools/software are designed to work on the UNIX/Linux command line. In the workshop today, you will learn how Linux/Unix is structured for storage, as well as interacting with the command line.

The [Unix](https://en.wikipedia.org/wiki/Unix) operating system has been around since 1969. Back then there was no such thing as a graphical user interface. You typed everything. It may seem archaic to use a keyboard to issue commands today, but it’s much easier to automate keyboard tasks than mouse tasks. There are several variants of Unix (including Linux), though the differences do not matter much for most basic functions. [Linux](https://en.wikipedia.org/wiki/Linux) was created in 1991 by a large community of contributors, but the main person who is given credit is [Linus Torvalds](https://en.wikipedia.org/wiki/Linus_Torvalds).

Increasingly, the raw output of research computing is usually in the form of large text files. Unix is particularly suited to working with such files and has several powerful (and flexible) commands that can process your data for you. The real strength of learning Unix is that most of these commands can be combined in an almost unlimited fashion. So if you can learn just five Unix commands, you will be able to do a lot more than just five things.

## Workshop Prerequisites

Before starting, make sure you have:

* A terminal on Linux, macOS, or WSL
* Access to the workshop server (if using remote resources)
* A stable internet connection for downloading training data
* Permission to install software in your home directory (for Conda)

## Two-Day Agenda

### Day 1: Beginner UNIX

* Filesystem organization and command anatomy
* Navigation and pathing
* Installing software with Conda
* Working with files and text
* Downloading and handling compressed data

### Day 2: Intermediate UNIX

* Batch file operations (`find`, `xargs`, `parallel`)
* Text processing (`grep`, `sed`, `awk`)
* Shell scripting for automation
* Remote computing and file transfer workflows

## Learning Outcomes

By the end of this workshop, you should be able to:

* Confidently navigate and manage files/directories from the terminal
* Inspect and transform text data with core Unix tools
* Build and run simple shell scripts for repetitive tasks
* Transfer and process data on remote/HPC systems using common best practices

## How to Use These Notes

* Run each command in your own terminal as you read.
* Use checkpoints and wrap-up pages to verify understanding before moving on.
* If a command fails, check spelling, case, and your current directory with `pwd`.

## Quick Links

* [Day 1 Wrap-up](advanced.md)
* [Text Processing](text_processing.md)
* [Shell Scripting](shell_scripting.md)
* [Remote Computing](remote_computing.md)
* [Day 2 Recap](intermediate_wrapup.md)

