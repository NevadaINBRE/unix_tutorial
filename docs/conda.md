# Software Installation (Conda)

At the end of the previous section, we tried to run the `tree` command. Did you get the expected output? No? Well, that is because you do not have the `tree` command installed. 

Since this is a shared computational resource and you do not have administrative rights to install new programs, we have to use a different program called `conda` which is able to install programs in your home directory so you can run them.

## 1. Why Use Conda?

Conda allows a user on a Unix system to install packages without sudo/admin privileges. Additionally, Conda allows users to create "environments" where a specific suite of programs are installed. A user can have as many environments as they want (i.e., for different projects and analyses types). This has the benefit of "freezing" your program versions so that your analyses are *reproducible*.

Imagine running an analysis, getting your final results, then 2 years later, your PI wants you to re-run the analysis with new samples. However, the programs you used were updated many times in the past years (bug fixes, new features, etc). Perhaps the output format has changed and now you can't compare the results of the new dataset with the old dataset. Using a Conda environment will allow you to recapitulate the original analysis for the new dataset.

## 2. Installing Conda

In order to install Conda, we will need to download the program directly to our remote Linux computer, Pronghorn, or on your local workstation. 

Visit this website here: [https://github.com/conda-forge/miniforge](https://github.com/conda-forge/miniforge).

If you scroll towards the bottom, you can see a list of Linux, OSX, and Windows installation options. We will use the one labeled "Linux X86_64". You can right-click and copy the link location from your browser, but I also provided the link below: [https://github.com/conda-forge/miniforge/releases/latest](https://github.com/conda-forge/miniforge/releases/latest).

We will use the `wget` utility, short for web get, to download this file to Pronghorn.

```bash
wget [https://github.com/conda-forge/miniforge/releases/download/24.9.2-0/Mambaforge-24.9.2-0-Linux-x86_64.sh](https://github.com/conda-forge/miniforge/releases/download/24.9.2-0/Mambaforge-24.9.2-0-Linux-x86_64.sh)
```

Next, we will run this file with the program `bash` in order to install Conda.

```bash
bash Mambaforge-24.9.2-0-Linux-x86_64.sh
```

There will be some prompts about the installation which we will proceed through together. In order to scroll through the EULA agreement, use the space bar. 

!!! warning "Reloading Configuration Required"
    Once done, you will need to reload the .bashrc configuration to get your new conda installation working. You can either restart your terminal or reload your environment. Please do this now after installing conda by typing `exit`.

!!! tip "Reloading the Environment"
    Instead of logging completely out and back in by typing `exit`, you can tell the terminal to refresh its settings immediately. Use the `source` command on your bash profile:

```bash
source ~/.bashrc
```

After reloading, test to see if Conda is working by using the `conda` command. If you do not receive a "command not found" error, you successfully installed Conda.



```bash
exit
```

After relogging, test to see if Conda is working by using the `conda` command. If you do not receive a "command not found" error, you successfully installed Conda.

## 3. Installing a Program with Conda

Now that we have `conda` installed, let's install the `tree` command. This will install `tree` into your currently loaded environment, which the default is "base". 

```bash
conda install -c conda-forge tree htop
```

**Understanding the Command:**

* **`conda install`**: The base command to download and install a new tool.
* **`-c conda-forge`**: Tells Conda to look in the `conda-forge` "channel" (repository) for the software.
* **`tree htop`**: The specific packages we want to install.

## 4. Monitoring Running Jobs

The `top` command prints a self-updating table of running processes and system stats. Use `q` to exit top, `z` to toggle better color contrast, `M` to sort by memory use, `P` to sort by processor use, and `c` to toggle display of the full commands. Hit `1` to toggle display of all processors, and hit `u` followed by typing in a username in order to only show processes (jobs) owned by that user.


Since we just installed it, let's also compare the new `htop` command to the system default `top` command.

!!! info "HPC Job Schedulers"
    While `htop` is an excellent tool for monitoring interactive sessions, keep in mind that you generally won't run resource-heavy bioinformatics tools directly on the login node. For heavy computational lifting, you will eventually package these commands into scripts and submit them to a cluster using job schedulers like SLURM or PBS.
