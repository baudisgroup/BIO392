# Introduction

Please run the tutorial at [SIB Course on UNIX](https://edu.sib.swiss/pluginfile.php/2878/mod_resource/content/4/couselab-html/content.html) first.

(Mind that we are running MacOS and not GNU/Linux)

# Set up

## Folders

Setting up a working directory with folders


```bash
cd  # goes to the home directory
mkdir -p course
ls -l course

mkdir -p course/soft course/data course/output

ls -l course

```

## Retrieving data

Downloading text files and checking their details.

```bash
cd  ~/course/data

curl http://gattaca.imppc.org/groups/maplab/imallona/teaching/example.bed \
   > example.bed

curl http://gattaca.imppc.org/groups/maplab/imallona/teaching/hg19.genome \
   > hg19.genome

file example.bed
ls -lah example.bed
head example.bed

file hg19.genome
ls -lah hg19.genome
head hg19.genome

```

## Executable software download

```bash

mkdir ~/course/soft/kent
cd ~/course/soft/kent

pwd  # printing current directory

curl http://hgdownload.soe.ucsc.edu/admin/exe/macOSX.x86_64/bedToBigBed \
   > bedToBigBed

file bedToBigBed

## adding exec permissions to the binary
chmod a+x bedToBigBed

# testing usage

## running the actual command
./bedToBigBed ../../data/example.bed ../../data/hg19.genome ~/course/output/out.bb

ls -lah ~/course/data/example.bed
ls -lah ~/course/output/out.bb
file ~/course/output/out.bb

```

## Software compiling (bedtools)

Retrieving source code and using a Makefile.


```bash

cd ~/course/soft
curl -L https://github.com/arq5x/bedtools2/releases/download/v2.25.0/bedtools-2.25.0.tar.gz \
  > bedtools-2.25.0.tar.gz

tar zxvf bedtools-2.25.0.tar.gz
cd bedtools2
make  ## will take time!
alias bedtools='~/course/soft/bedtools2/bin/bedtools'

bedtools --help

```

# Exercise 1

Find the number of lines of the file `~/course/data/example.bed`


<details><summary>Answer</summary>
<p>

Note the `-l` flag
```bash
wc -l ~/course/data/example.bed
```

</p>
</details>


# Exercise 2

Get the manual page of `head`, what is this command for?

<details><summary>Answer</summary>
<p>

```bash
man head
```

</p>
</details>

# Exercise 3

Get the first 5 lines of the file `~/course/data/example.bed`

<details><summary>Answer</summary>
<p>

```bash
head -5 ~/course/data/example.bed
```

</p>
</details>

# Exercise 4

Using pipes (`|`): chain the `head -5` command before with  `wc -l` to make sure the number of lines was as expected (5).



<details><summary>Answer</summary>
<p>

```bash
head -5 ~/course/data/example.bed | wc -l
```

</p>
</details>


# FASTQ/A Exercises

DISCLAIMER: data is based upon the Genome Analysis Workshop (MOLB 7621) Course on genome analysis at the University of Colorado SOM at [https://github.com/MOLB7621](https://github.com/MOLB7621)

## Exercise 5

Retrieve the data for these exercises (mind the path you're working at, should be `~/course/data`).

The path for URL for the data is https://molb7621.github.io/workshop/_downloads/SP1.fq



<details><summary>
Answer
</summary>

<p>

```bash
cd ~/course/data
curl -L https://molb7621.github.io/workshop/_downloads/SP1.fq  \
   > SP1.fq
```

</p>
</details>


##  Exercise 6

Inspect the file from the previous exercise (file size, number of lines, and visualize its head)



<details><summary>
Answer
</summary>

<p>

```bash
cd  ~/course/data
ls -lh SP1.fq
wc -l SP1.fq

head SP1.fq
```

</p>
</details>

## Exercise 7


