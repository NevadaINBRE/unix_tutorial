# Downloading and Compression

## 1. Downloading Data with Terminal

Let's start working with some compressed data now. First, let's change directories to our training folder then download a FASTA file for the SARS-CoV-2 genome reference.

To do this, we will use the `wget` command to download a file from the internet: [http://ftp.ensemblgenomes.org/pub/viruses/fasta/sars_cov_2/dna/Sars_cov_2.ASM985889v3.dna.toplevel.fa.gz](http://ftp.ensemblgenomes.org/pub/viruses/fasta/sars_cov_2/dna/Sars_cov_2.ASM985889v3.dna.toplevel.fa.gz).

```bash
cd training
wget [http://ftp.ensemblgenomes.org/pub/viruses/fasta/sars_cov_2/dna/Sars_cov_2.ASM985889v3.dna.toplevel.fa.gz](http://ftp.ensemblgenomes.org/pub/viruses/fasta/sars_cov_2/dna/Sars_cov_2.ASM985889v3.dna.toplevel.fa.gz)
```

## 2. Working with Compressed Data

Now, try to view the contents of the genome reference with `cat`.

```bash
cat Sars_cov_2.ASM985889v3.dna.toplevel.fa.gz 
```

This file doesn't make much sense. It's because the file is compressed which is signified by the file extension ".gz". We can view compressed files with the `zless` command, much like we did with `less`. Other commands operate with compressed files in a similar manner: `zcat`, `zgrep`.

```bash
zless Sars_cov_2.ASM985889v3.dna.toplevel.fa.gz 
```

How do I uncompress the file? We can use the `gunzip` command to uncompress the file.

```bash
gunzip Sars_cov_2.ASM985889v3.dna.toplevel.fa.gz 
ls
```

You will notice that the `.gz` file extension was taken off afterwards. Now you can try using `cat` to view the file and you should not get the weird characters like before.

```bash
cat Sars_cov_2.ASM985889v3.dna.toplevel.fa
```

Let's use `grep` to count how many sequences we have in this file. Since we know each reference contig/chromosome contains the `>` character and then name information, we can have `grep` search for the header lines.

!!! warning "Watch Your Quotes!"
    Remember the `>` character is used for redirection too. **Be sure to surround it with quotes, if not you will accidentally overwrite your genome reference file.**

```bash
grep ">" Sars_cov_2.ASM985889v3.dna.toplevel.fa
```

This result means there is 1 contig/chromosome for this genome. We could use the `-c` option with `grep` so that it displays the count, rather than outputs the text.

```bash
grep -c ">" Sars_cov_2.ASM985889v3.dna.toplevel.fa
```

## 3. Downloading Sequencing Data

Now that we have a genome reference file and explored it, let's download sequencing data from an Illumina machine which millions of reads.

Link: [https://biox.unr.edu/ftp/biox_microbiome_workshop/SRR19195566_covid_sra_data.fastq.gz](https://biox.unr.edu/ftp/biox_microbiome_workshop/SRR19195566_covid_sra_data.fastq.gz)

This is hosted on our webserver. However, there is a web certificate problem, so we will need to use the `--no-check-certificate` command line option to allow `wget` to download the file.

```bash
wget --no-check-certificate [https://biox.unr.edu/ftp/biox_microbiome_workshop/SRR19195566_covid_sra_data.fastq.gz](https://biox.unr.edu/ftp/biox_microbiome_workshop/SRR19195566_covid_sra_data.fastq.gz)
```

Look at the file sizes of the files with the `ls` command.

```bash
ls -alh
```

Notice, our uncompressed genome file is 30K or 30 kilobytes. The fastq sequencing file we just downloaded is compressed (`.gz`) and it is still 141M or 141 megabytes. We do not want to uncompress the file as we did before, because the file will inflate to a much larger file. Instead, we will be using compressed compatible commands, as discussed previously.

Let's view the file using `zless`.

```bash
zless SRR19195566_covid_sra_data.fastq.gz
```

This is an example of the first read from this file. The first line is the header line which has the name information of the read. The second line is the sequence line which has the string of "A,C,G,T"s. The third line is another header line. The fourth line contains the sequence quality information.

Let's use `grep` to count how many sequences we have in this file. Since we know that each set of 4 lines contains information for 1 read .... and each of the four lines starts with a `@` in the header, let's use the `@` character with `grep` to count the number of reads we have in this file. Remember to use `zgrep` instead of `grep` because this is a compressed file.

```bash
zgrep -c "@" SRR19195566_covid_sra_data.fastq.gz 
```

Can you think of another way to count the number of sequences?

```bash
zcat SRR19195566_covid_sra_data.fastq.gz | grep "@" | wc -l
```

There are many ways to get to the same result! This is true for many bioinformatic analyses.
