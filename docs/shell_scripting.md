# Shell Scripting for Automation

## Learning Objectives

By the end of this section, you should be able to:

* Build and run a basic Bash script with a shebang
* Pass arguments into a script and validate inputs
* Use quoting and variables safely
* Capture output and errors separately for debugging

In this section, we will be writing our first BASH script using a similar method we explored in the first section today.

First, in order to write this script, we need to have a list of files we want to process. We can use find again to create this list then start editing our first BASH script.

```bash
(base) [user@login-0 testdata1]$ find . -type f -iname "*.fastq.gz"  > files.txt
(base) [user@login-0 testdata1]$ nano filelist_linecounter.sh
```

Include the following text in the script, then save. We will discuss the meaning of each line. In this BASH script, we use variables (INFILE) and a for loop to process this data.

```bash
#!/usr/bin/env bash

INFILE=files.txt
IFS=$'\n' # set the Internal Field Separator to newline

for file in $(cat "$INFILE")
do
    wc=`zcat $file | wc -l`
    echo $file: $wc
done
```

## Variables, Quoting, and Safer Loops

When working with filenames, always use quotes around variables. This prevents breakage when paths contain spaces or special characters.

Safer pattern for reading a list of files:

```bash
while IFS= read -r file; do
    wc=$(zcat "$file" | wc -l)
    echo "$file: $wc"
done < "$INFILE"
```

This avoids command substitution on entire file lists and handles edge cases more reliably.

To run this BASH script, execute it with:

```bash
bash filelist_linecounter.sh
```

Our script worked! Now, let's modify it to allow for any filename, rather than a files.txt name specifically.

```bash
#!/usr/bin/env bash

# Ensure a file list is provided
if [ "$#" -ne 1 ]; then
    echo "Usage: $0 <file_list>"
    exit 1
fi

INFILE="$1"
IFS=$'\n' # Set the Internal Field Separator to newline

# Iterate through each line in the file
for file in $(cat "$INFILE"); do
    wc=$(zcat "$file" | wc -l)
    echo "$file: $wc"
done
```

Great, we have successfully generalized our BASH script! This may be a useful utility for later, so let's set it up as a custom executable command. First, we need to create a bin directory in our home folder, then mv this script into the folder and make it executable.

```bash
mkdir -p ~/bin
mv filelist_linecounter.sh ~/bin/
chmod +x ~/bin/filelist_linecounter.sh
```

Some Operating Systems will include `~/bin` within the `$PATH` variable to execute programs. Let's ensure this is configured, if not add it to our configuration file.  On most UNIX systems, this is `~/.bashrc`.  However, on MAC OSX this file is named `~/.zshrc`.

```bash
nano ~/.bashrc
# OR
nano ~/.zshrc
```

Now look for a line that contains `export PATH=.....` and make sure it lists your home directory bin folder.

```bash
export PATH="/data/gpfs/home/user/bin:$PATH"
```

Now, you should be able to run that script from any directory, not just the directory where we moved the script. Notice the `bash` command is not used when running the script directly. This works because the first line (shebang line) tells the script which interpreter to use.

```bash
(base) [user@login-0 testdata1]$ filelist_linecounter.sh files.txt 
./set1/SRR5130759_1.fastq.gz: 2137316
./set1/SRR5130756_1.fastq.gz: 2681968
./set1/SRR5130755_1.fastq.gz: 2223852
```

#### Redirecting input/output and error handling: STDOUT & STDERR

Programs can write to two separate output streams, ‘standard out’ (STDOUT), and ‘standard error’ (STDERR). The former is generally for direct output of a program, while the latter is supposed to be used for reporting problems. 

!!! note "A quirk about STDERR"
    I’ve seen some bioinformatics tools use STDERR to report summary statistics about the output, but this is generally considered bad practice. It is supposed to be used strictly for reporting problems. Default behavior in a lot of cases is to dump both STDOUT and STDERR to the screen, unless you specify otherwise.

!!! tip "Always Save STDERR on Remote Servers"
    Saving STDOUT is pretty routine (you want your results, yes?), but remember that explicitly saving STDERR is critical on a remote server, since you may not directly see the ‘screen’ when you’re running jobs in the background.

```bash
(base) [user@login-0 testdata1]$ wc -c Escherichia_coli_w_gca_000184185.ASM18418v1.60.gff3 1> chars.txt 2> any.err
```

* The 1st output, **STDOUT**, goes to ‘chars.txt’
* The 2nd output, **STDERR**, goes to ‘any.err’

```bash
(base) [user@login-0 testdata1]$  cat chars.txt
```
Contains the character count of the file.

```bash
(base) [user@login-0 testdata1]$ cat any.err
```
Empty since no errors occurred.

Let's rerun again, this time with the filename misspelled.

```bash
(base) [user@login-0 testdata1]$ wc -c Escherichia_coli_w_gca_000184185.ASM18418v1.60.g 1> chars.txt 2> any.err
(base) [user@login-0 testdata1]$ cat chars.txt 
(base) [user@login-0 testdata1]$ cat any.err 
wc: Escherichia_coli_w_gca_000184185.ASM18418v1.60.g: No such file or directory
```

#### Scaling Up: Workflow Managers (Nextflow & Snakemake)

Writing BASH scripts with `for` loops is a fantastic way to automate processing for a handful of files. However, as your projects grow (e.g., processing hundreds of whole-genome or microbiome samples), simple shell scripts hit a few limitations:

1. **Error Handling:** If a loop fails on sample 50 of 100, restarting the script usually means re-running the first 49 samples unless you write complex checkpointing logic.
2. **Parallelization:** While we used `parallel` earlier, managing complex dependency chains (e.g., Step B must wait for Step A, but Step C can run anytime) is very difficult in pure BASH.
3. **Reproducibility:** Sharing a complex BASH script across different computer systems can lead to broken paths and missing software.

To solve this, the bioinformatics and data science communities rely on **Workflow Managers**, with [Nextflow](https://www.nextflow.io/) and [Snakemake](https://snakemake.github.io/) being the two most prominent industry standards.

!!! tip "Why learn a Workflow Manager?"
    Tools like Nextflow and Snakemake allow you to define your pipeline as a series of modular steps. If a job fails, they automatically know how to resume *exactly* where they left off. They also seamlessly handle software environments (like Docker containers) and can automatically submit your jobs to high-performance computing schedulers without changing your core code.
