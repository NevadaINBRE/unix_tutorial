### Welcome to Day 2: Automation & Scaling

![](https://assets.techrepublic.com/uploads/2021/08/tux-new.jpg)

Welcome back! Previously, we established the foundational skills needed to navigate the command line, manage directories, and install software. Today, we are shifting our focus from *navigating* to *automating*. 

In modern research computing, especially when dealing with massive data outputs like high-throughput sequencing or complex array jobs, manually running commands one by one simply doesn't scale. The raw output of these experiments is usually in the form of large text files spread across complex directory structures. 

The true power of the Unix command line lies in its ability to string together small, highly optimized tools to do the heavy lifting for you. In this advanced session, you will learn how to:

* **Batch Process:** Execute commands across thousands of files simultaneously.
* **Parse Text:** Extract, filter, and compute data directly from large files without ever opening them.
* **Automate:** Write reusable shell scripts so you never have to type the same complex command twice.
* **Compute Remotely:** Securely transfer your data and keep your pipelines running on High-Performance Computing (HPC) clusters, even after you close your laptop. 

If you can master just a handful of these advanced commands, you will fundamentally change how quickly and efficiently you can process your research data. Let's dive in.

### Advanced File & Directory Management

#### Batch processing & working with multiple files

Often times researchers have to deal with large datasets comprised of a lot of smaller individual files. Linux has some utilities to help work with multiple files in an automated fashion. The utilities we will be covering in this section are: `find`, `xargs`, `parallel`, for loops in BASH, symlinks/hardlinks, `du`, `df`, and `ncdu`.

First, we need to download the example data to a folder for this training. Let's all create a `unix_adv_workshop` folder and transfer the example data into there. This dataset is a Bioinformatics dataset of gut microbiome sequencing. The data is compressed in .gz format.

```bash
mkdir unix_adv_workshop
cd unix_adv_workshop
wget https://biox.unr.edu/ftp/workshop_data/testdata1.tar.gz
tar -xvzf testdata1.tar.gz
cd testdata1
```

Let's use the find command to find data files within this directory structure. We can use the find command with the `*` glob character in order to match anything that would replace the `*`.  We want to find all files that end with .gz, therefore, we would use `*.gz` in the find command.

```bash
find . -name "*.gz"
```

Let's review the output for this command. You will notice it each line contains the location to one of the files that matches the *.gz pattern.  However, you may notice this data isn't organized very well.  Some of the sets have the data at the top directory, some have multiple layers of sub directories where the .gz files are located. Also, notice that some files end with .fastq.gz and others with .txt.gz.  

Let's update the command to only operate on .fastq.gz files.

```bash
find . -type f -name  "*.fastq.gz"
```

Review the output and ensure that only .fastq.gz files are printed.

Now that we are finding our correct files, we want to get some preliminary Quality Control information about this data. We can have find run a utility for each file that gets returned.  In this case, we want to run ls for each file so that we can get the file sizes for the dataset. 

In order to do this, we will be adding the `--exec` flag which then executes the next command line utility listed with any parameters also. The file name `find` found will be replaced with the {} characters. Then the `\;` signifies to find that is the end of the command.

```bash
find . -type f -name "*.fastq.gz" -exec ls -lh {} \;
```

Great, we have a list of filesizes for everything! However, what if some of the files had different cases then what we wrote in the -name search?  Since UNIX is case sensitive, our find command would not find those files.  Instead, we can use the -iname option to use CASE INSENSITIVE search.

```bash
find . -type f -iname "*.fastq.gz" -exec ls -lh {} \;
```

Now, let's try to get the number of lines in the file with the utility `wc -l`.  However, our data is compressed, so we would like to use two utilities in order to keep the data compressed but calculate the number of lines. `zcat` to uncompress the data on the fly then piped into `wc -l` to count the number of lines in the file. 

```bash
find . -type f -name "*.fastq.gz" --exec zcat {} | wc -l \;
```

You will notice this command doesn't work. Find can't work with pipes, but there is an alternative! We can use `xargs` or `parallel` to run these more complex commands after find.

```bash
find . -type f -iname "*.fastq.gz" -print0 | xargs -0 -I {} sh -c 'echo -n "{}: "; zcat "{}" | wc -l'
find . -type f -iname "*.fastq.gz" | parallel 'echo -n "{}: "; zcat "{}" | wc -l'
```

Lets break down each of these commands:

* **`xargs`:**
    ```bash
    find . -iname "*.fastq.gz" -print0 | xargs -0 -I {} sh -c 'echo -n "{}: "; zcat "{}" | wc -l'
    ```
    A similar find command was used as in previous examples. `-print0` is added to allow find to pass filenames with spaces and special characters to the next command. `| xargs -0` pipes the results from find into the `xargs` program. `-0` allows `xargs` to read null-terminated filenames. `-I {}` replaces the `{}` characters with the filenames. `sh -c '...'` runs the `sh` shell to execute the command following `-c`. 

* **`parallel`:**
    ```bash
    find . -iname "*.fastq.gz" | parallel 'echo -n "{}: "; zcat "{}" | wc -l'
    ```
    A similar find command was used as in previous examples. Notice there is less syntax than the `xargs` example above. This takes the results from the `find` command, pipes it, and then runs `parallel` to execute the command within the single quotes.

Parallel was designed after xargs to fix some of it's limitations. Therefore, to parallelize commands, I suggest using parallel which we will be going over more options next.

You will notice in the previous two executions, `xargs` took a while to run, where as the `parallel` command ran much quicker. This is because by default, `parallel` uses all CPU cores on a system. In contrast, `xargs` defaults to only using 1 core.  You can customize these options with the following commands.

```bash
find . -type f -iname "*.fastq.gz" -print0 | xargs -0 -P 2 -I {} sh -c 'echo -n "{}: "; zcat "{}" | wc -l'
find . -type f -iname "*.fastq.gz"  | parallel -j 2 'echo -n "{}: "; zcat "{}" | wc -l'
```

For `xargs`, the `-P 2` tells it to use 4 cores for processing. Likewise for `paralel`, the `-j 2` tells it to use 4 cores for processing. However, carefully look at how the data gets printed to the screen for each execution. Notice how the `xargs` command prints the echo text right away before including the line count results.  Where as `parallel` has the data organized in the expected manner.

Now, let's say we want to run the same command, using half of our computer's resources. How would we do that for both `xargs` and `parallel`?

```bash
find . -type f -iname "*.fastq.gz" -print0 | xargs -0 -P $(($(nproc) / 2)) -I {} sh -c 'echo -n "{}: "; zcat "{}" | wc -l'
find . -type f -iname "*.fastq.gz"  | parallel -j 50% 'echo -n "{}: "; zcat "{}" | wc -l'
```

For `xargs`, we have to use the `nproc` utility to get the total number of cores on our system, then divide that result by 2 to configure xargs to use 50% of our cores.
For `parallel`, we simply have to configure it by changing the `-j` option to `-j 50%`. 

However, notice that parallel keeps the echo filename: wc -l results paired, where as xargs does not. When you need to print the results of the command to screen, parallel handles this better. However, if you are running a data processing command with output files, either way of running the commands would work. Later in the workshop, we will write a BASH script to do a similar process. 

Now, let's clean up the empty files. For any lines that have "gzip: ./set4/SRR5130826_1.fastq.gz: unexpected end of file", please delete these files, then rerun the wc -l commands to make sure the data has been cleaned up. Now that we have a list of files, we could manually run the `rm filename` command, but let's have find delete these files for us!

```bash
find . -type f -iname "*.fastq.gz" -size 0 -delete
find . -type f -iname "*.fastq.gz"  | parallel -j 50% 'echo -n "{}: "; zcat "{}" | wc -l'
```

#### Symbolic Links

Since copying or even moving large files (like sequence data) around your filesystem may be impractical, we can use links to reference ‘distant’ files without duplicating the data in the files. Symbolic links are disposable pointers that refer to other files, but behave like the referenced files in commands.

!!! tip "Best Practice: Default to Symbolic Links"
    You should, by default, always use a symbolic (`-s`) link when working with sequence data.

Hard links point to the location of the data on the hard drive while symbolic links point to a secondary location of the data on the hard drive (or the original file itself).

![](https://ucdavis-bioinformatics-training.github.io/2019-Winter-Bioinformatics_Command_Line_and_R_Prerequisites_Workshop/CLI_Intro/hard_vs_symbolic.png)

!!! warning "The Drawback of Hard Links"
    Deleting the original file becomes much more difficult with hard links because all hard links need to be deleted first before the space is actually freed up.


```bash
(base) [user@login-0 testdata1]$ mkdir raw_reads
(base) [user@login-0 testdata1]$ find . -type f -iname "*.fastq.gz"  -exec ln -s {} raw_reads/ \;
(base) [user@login-0 testdata1]$ ls -alh raw_reads/
total 119K
drwxr-sr-x 2 user rc-group 8.0K Mar 21 13:31 .
drwxr-sr-x 7 user rc-group 8.0K Mar 21 13:29 ..
lrwxrwxrwx 1 user rc-group   28 Mar 21 13:31 SRR5130755_1.fastq.gz -> ./set1/SRR5130755_1.fastq.gz
lrwxrwxrwx 1 user rc-group   33 Mar 21 13:31 SRR5130761_1.fastq.gz -> ./set2/dir1/SRR5130761_1.fastq.gz
```

These linked files are blinking in red. It's because the symlink is relative, indicated by the `->` pointer to the linked file. In order to correctly symlink this data, we have to include the absolute path, rather than relative path.

```bash
(base) [user@login-0 testdata1]$ find $PWD -type f -iname "*.fastq.gz"  -exec ln -s {} raw_reads/ \;
(base) [user@login-0 testdata1]$ ls -alh raw_reads/
total 119K
drwxr-sr-x 2 user rc-group 8.0K Mar 21 13:33 .
drwxr-sr-x 7 user rc-group 8.0K Mar 21 13:29 ..
lrwxrwxrwx 1 user rc-group   92 Mar 21 13:33 SRR5130755_1.fastq.gz -> /data/gpfs/projects/testdata1/set1/SRR5130755_1.fastq.gz
```

These linked files are not blinking red. This time the symlink points to an absolute path on the filesystem where the file is located. Now, we can treat these files as if they are present in the current directory.

#### Disk usage analysis: du, df, and ncdu

Monitoring disk usage is very important when processing data. Filesystems can quickly fill up when processing many samples. In order to monitor usage, UNIX provides two utilities: df and du.

The `df` utility stands for "disk free".  This is used to find disk usage for any filesystems connected to the computer. 

```bash
(base) user@node:~$ df -h
Filesystem              Size  Used Avail Use% Mounted on
udev                     63G     0   63G   0% /dev
tmpfs                    13G  3.4M   13G   1% /run
/dev/nvme0n1p2          1.8T  1.1T  682G  61% /
```

The `du` utility stands for "disk usage". This allows a user to summarize a directory or directory structure with total storage size of each directory.  Let's run it on our test data.

```bash
(base) [user@login-0 testdata1]$ du -h
103K    ./raw_reads
140M    ./set1
239M    ./set2
```

The `-h` option outputs the storage counts in human readable format.  We can also use the `-s` option to summarize only the folder described, rather than the directory structure.

```bash
(base) [user@login-0 testdata1]$ du -sh .
2.5G    .
(base) [user@login-0 testdata1]$ du -sh set1/
140M    set1/
```

The last utility is not installed by default on systems: `ncdu`.  Let's install this utility with conda and explore using it.

```bash
(base) [user@login-0 testdata1]$ conda install ncdu
# OR
(base) [user@login-0 testdata1]$ mamba install ncdu
(base) [user@login-0 testdata1]$ ncdu
```
