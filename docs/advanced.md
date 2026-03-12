# Advanced Tools and Wrap-up

## 1. STDOUT & STDERR

Programs can write to two separate output streams, ‘standard out’ (STDOUT), and ‘standard error’ (STDERR). The former is generally for direct output of a program, while the latter is supposed to be used for reporting problems. I’ve seen some bioinformatics tools use STDERR to report summary statistics about the output, but this is probably bad practice. Default behavior in a lot of cases is to dump both STDOUT and STDERR to the screen, unless you specify otherwise. 

In order to nail down what goes where, and record it for posterity:

```bash
wc -c second.txt 1> chars.txt 2> any.err
```

**Understanding the Redirection:**

* **`1>`**: The 1st output, STDOUT, goes to ‘chars.txt’.
* **`2>`**: The 2nd output, STDERR, goes to ‘any.err’.

```bash
cat chars.txt
```

Contains the character count of the file `second.txt`.

```bash
cat any.err
```

Empty since no errors occurred.

Saving STDOUT is pretty routine (you want your results, yes?), but remember that explicitly saving STDERR is important on a remote server, since you may not directly see the ‘screen’ when you’re running jobs.

## 2. Using the Screen Command

This section will show you how to configure and use screen.

When you connect to a remote server via ssh/PUTTY, you are presented one window to do all your work. However, there are times where you may be editing a file and want to look up a file path/location in order to configure it within your script you are writing. In order to enable this, you can use the `screen` command. Screen will create virtual windows on the remote system so you can perform multiple actions at the same time.

First, we need to download a screen configuration file in our home directory called `.screenrc`. I have one hosted on our core's github page here: [https://raw.githubusercontent.com/Nevada-Bioinformatics-Center/unix_configurations/main/.screenrc](https://raw.githubusercontent.com/Nevada-Bioinformatics-Center/unix_configurations/main/.screenrc).

Use `wget` to download this file to your home directory.

```bash
wget [https://raw.githubusercontent.com/Nevada-Bioinformatics-Center/unix_configurations/main/.screenrc](https://raw.githubusercontent.com/Nevada-Bioinformatics-Center/unix_configurations/main/.screenrc)
```

Now that we have the configuration file, let's start a session:

```bash
screen
```

Now that you are running screen, you will see the bottom portion of your terminal has some new text. In green towards the bottom left of your screen, you have the hostname of the computer you are on, `[login-1]` in my case. Next to this you have your virtual windows numbered from 0 to N. By default right after you run screen, you will have 1 named window as `(0*$bash)`.

Let's create a new screen window by pressing the `CTRL+A` keys together. These two keys together tells the server that the next key that is pressed will be a screen command. Press the `c` key after you press `CTRL+A` keys. This will Create a new window labeled `(1*$bash)`.

You can then switch between these virtual windows by pressing the `CTRL+A` keys together then typing the number of the window you want to view. Test this now.

When you are finished working, you can close your terminal to end your session. Then, the next day you want to work on the project you can SSH to Pronghorn and then issue the `screen -x` command to restore your previous screen session. Pretty Nifty!

**Screen cheat sheet:**

![](https://jeffkayser.com/static/cheatsheets/cheatsheet_screen.png) 

## 3. Workshop RECAP

Now, you should know how to use the following commands: `ls`, `cd`, `cp`, `nano`, `tree`, `rm`, `mkdir`, `cat`, `wc`, `grep`, `echo`, `less`, `more`.

If you ever forget how to specifically use a command, use the `man` command followed by the command you want to look up, to pull up a help manual. Try this now with the `cp` command.

```bash
man cp
```

This will conclude our first day.

**Below is a BASH commands cheat sheet:**

![](https://www.git-tower.com/blog/media/pages/posts/command-line-cheat-sheet/7c0f8706c6-1676567324/command-line-cheat-sheet-large01.png) 

![](https://www.git-tower.com/blog/media/pages/posts/command-line-cheat-sheet/44cf1bb4ec-1676567324/command-line-cheat-sheet-large02.png)

## 4. Transitioning to Pipelines

As you get comfortable with these commands, you will quickly realize that typing them one by one interactively is just the beginning. 

The raw UNIX commands you learned today are the exact foundational building blocks used to write reproducible scripts and build automated workflows using managers like Nextflow and Snakemake. Mastering the terminal today gives you the exact skills you need to build the pipelines of tomorrow!
