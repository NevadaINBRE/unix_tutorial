# Working with Files

## Learning Objectives

By the end of this section, you should be able to:

* Create, edit, copy, and remove files/directories
* Use redirection safely (`>`, `>>`) when combining outputs
* Search and summarize text with `grep` and `wc`
* Understand basic Unix file permissions and executable bits

Since we just re-logged into the server, you will be in your HOME directory. Change directories to your training folder.

```bash
cd training
```

!!! tip "The Escape Hatch: CTRL+C"
    If you ever run a command that hangs, or you accidentally type a single quote (`'`) and hit enter, you might get trapped in a secondary prompt (`>`) with no idea how to escape. Do not panic! Pressing `CTRL + C` safely cancels the current command and returns you to a fresh, clean prompt.

## 1. Editing with Nano

Let's add some text to the `data.txt` file using the `nano` command. 

```bash
cd data/
nano data.txt
```

I will add the sentence "This is our first data file.". Then press Enter and add a second sentence "Unix is fun!".

!!! tip "Navigating Nano"
    Nano does not accept user mouse clicks to navigate within the program. You will need to use arrow keys to move the text cursor.

To save the file, use the keys `CTRL + o`. Nano will then verify the filename towards the bottom of the screen, press Enter. Then press `CTRL + x` to exit.

## 2. Copying and Redirecting Files

Make a copy of the file using the `cp` command and name the copy `second.txt`. Similar to move, this command takes two parameters: the source file and the destination file.

```bash
cp data.txt second.txt
ls
```

Edit the `second.txt` file and change "first data file" to "second data file". Use the `cat` command to verify the changes. The `cat` command outputs the file contents to the terminal.

```bash
nano second.txt
cat data.txt
cat second.txt
```

We are going to use "redirection" to create a third file with the contents of both of these text files. Any command's output can be saved to a file using the `>` symbol followed by the filename you want to save the output as. This will overwrite the file, if already created. However, you can also use the `>>` symbol to append the contents to a file, which will not overwrite the file.

```bash
cat data.txt > third.txt
cat second.txt >> third.txt
cat third.txt
```

We could make the same file in one command by using `cat` with multiple files and redirection. Let's call this file `fourth.txt`.

```bash
cat data.txt second.txt > fourth.txt
```

## Redirection Quick Reference

* `>` overwrite output file
* `>>` append to output file
* `2>` write error output to file

Example:

```bash
wc -l data.txt 1> line_count.txt 2> line_count.err
```

## 3. Word Count and Searching

Let's get some basic text file information using the `wc` command, which stands for word count. `wc` will output the number of lines, words, and bytes of a file.

```bash
wc data.txt
wc third.txt
```

What if you're only interested in counting the lines? You can use the `-l` option to only count the number of lines.

```bash
wc -l data.txt
```

If we had a large file and wanted to search for a specific keyword, we can search through the file using the `grep` command. `grep` will return the text line for every instance the keyword is found. 

Try searching for "Unix" in the "data.txt" and "third.txt" files.

```bash
grep "Unix" data.txt
grep "Unix" third.txt
```

## 4. Environment Variables

One use of the `echo` command is for displaying the contents of something known as environment variables. These contain user-specific or system-wide values that either reflect simple pieces of information (your username), or lists of useful locations on the file system.

```bash
echo $USER
echo $HOME
echo $PATH
```

The last one shows the content of the `$PATH` environment variable, which displays a — colon separated — list of directories that are expected to contain programs that you can run. This includes all of the Unix commands that you have seen so far. Knowing how to change your `$PATH` to include custom directories can be necessary sometimes (e.g. if you install some new bioinformatics software in a non-standard location).

## 5. Removing Files and Folders

Since `third.txt` and `fourth.txt` are essentially the same file, let's remove the file `fourth.txt` with the `rm` command.

```bash
rm fourth.txt 
ls
```

Let's make a new folder called `extra` and move the `third.txt` file in it.

```bash
mkdir extra
mv third.txt extra/
ls
```

Try to remove the `extra` folder with the `rm` command like we used to remove the previous file.

```bash
rm extra/
```

You will receive an error because `extra` is a directory. The `rm` utility tries to protect users from accidentally removing full folders by doing this. However, we can use command line flags, similar to what we covered in `ls`, to force `rm` to remove the directory. These flags are `-f` for force and `-r` for recursive. Recursive means to include any sub-folders as well.

```bash
rm -r -f extra/
ls
```

## File Permissions Basics (rwx, chmod)

Use `ls -l` to inspect file permissions:

```bash
ls -l
```

You will see permission groups for user, group, and others:

* `r` = read
* `w` = write
* `x` = execute

To make a script executable later in the workshop:

```bash
chmod +x my_script.sh
```

## 6. Piping and History

You can view a history of all the commands ran during your terminal session. Type `history` to view.

```bash
history
```

You can rerun a particular command by using the bang (`!`) and then the number of the command you want to rerun. Example: `!2092`. You can also rerun a particular command by using the bang (`!`) then the first few letters of the command. Example: `!grep`.

When you run the history command, you will notice **a lot** of text gets printed to the screen. We can use the `|` pipe character which is below backspace and above enter. When using the `|`, it tells Unix to take the output from the first command and use it for the second command. 

Let's do this with `history` and `less` (a utility that allows paging through files).

```bash
history | less
```

## Day 1 Checkpoint 3

Before continuing, verify you can:

* Create and edit a file with `nano`.
* Merge content from two files using redirection.
* Find lines containing a keyword with `grep`.
* Explain what `chmod +x` does.
