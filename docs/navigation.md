# Navigation and Pathing

## 1. Command Line Options & TAB Completion

Notice the `ls` command lists the files. However, sometimes we want more information than just what is in a folder. For example, we may want to know file timestamps, file sizes, and user permissions.

To get more information, we will use command line flags which will change the way `ls` operates. Try running the `ls -alh` command. 

```bash
ls -alh
```

**Understanding the Command:**

* **`-a`**: The "a" in the flags will list all files.
* **`-l`**: The "l" means to use the long format.
* **`-h`**: The "h" means to output the file sizes in human readable format, rather than just bytes.

Let's say we want to see if we have any files in our `.ssh` folder. We can list files in the `.ssh` folder by typing `.ssh` after `ls` to specify we want to list files in that folder.

```bash
ls .ssh
```

Since the `.ssh` folder is in our home folder, we can use **TAB COMPLETION** to auto-complete the name. Test this by typing `ls .s` then press TAB and you will see the rest of the name auto-complete.

!!! warning "Case Sensitivity"
    Now try typing `ls .S` and press TAB. Did anything happen? No, this is because in unix files/folders/commands are **CASE SENSITIVE**.

## 2. Relative vs Absolute Pathing

So far, we have only used examples of relative pathing, which means the file/folder path relative to the current directory we are in. 

An example of absolute pathing would be the string that got returned when we used the `pwd` command at the beginning. Notice, the path starts with the `/` character, which means the root (top) of the computer.

Let's try to list files in the HOME folder using absolute paths and TAB completion. TAB will only complete up until the result has multiple options. For example, if you TAB complete the first letter of your username, it probably won't return anything because there are other users with the same name. You can press TAB twice quickly to have it list the different options.

Let's use relative pathing to list the files above our home folder. In Unix, the `..` characters signify up a directory whereas `.` signifies the current directory. Since I know this is going to be a long list, I can use the `-1` option for `ls` to create a single line list.

```bash
ls -1 ..
```

## 3. Changing Directories and Working with Files

In order to change directories, use the `cd` command. Let's navigate into the `training` folder we made earlier.

```bash
cd training/
```

Notice that the path on the terminal updated from `~` to `~/training`. This helps the user know where they are located on the computer.

Let's make an example file called `test.txt` using the `touch` command and then verify it was created with the `ls` command. Touch will create a new empty file or update the timestamp for a given file.

```bash
touch test.txt
ls
```

Let's rename the file to `data.txt` using the `mv` command. The `mv` command takes two parameters: the source file and the destination file. So, to rename the file `test.txt` to `data.txt`, use the following command.

```bash
mv test.txt data.txt
ls
```

Now, we want to organize this folder by putting the `data.txt` file into a new sub-folder called `data`. Try to figure out how to do that by using a previous command to create the directory then the `mv` command to move the `data.txt` folder into the data folder.

```bash
mkdir data
mv data.txt data
ls data/
```

We can use the `tree` command to visualize the directory tree.

```bash
tree
```
