# Introduction

Please run the tutorial at [SIB Course on UNIX](https://edu.sib.swiss/pluginfile.php/2878/mod_resource/content/4/couselab-html/content.html) first.

(Mind that we are running MacOS and not GNU/Linux)

For extra/advanced reading, please check the following tutorials on bash and awk:

* [Shell](http://www.grymoire.com/Unix/Sh.html)
* [Awk](http://www.grymoire.com/Unix/Awk.html)

# Set up

## Folders

Setting up a working directory with folders


```bash
cd ~ # goes to the home directory
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

## Exercise 1

Find the number of lines of the file `~/course/data/example.bed`


<details><summary>Answer</summary>
<p>

Note the `-l` flag
```bash
wc -l ~/course/data/example.bed
```

</p>
</details>


## Exercise 2

Get the manual page of `head`, what is this command for?

<details><summary>Answer</summary>
<p>

```bash
man head
```

</p>
</details>

## Exercise 3

Get the first 5 lines of the file `~/course/data/example.bed`

<details><summary>Answer</summary>
<p>

```bash
head -5 ~/course/data/example.bed
```

</p>
</details>

## Exercise 4

Using pipes (`|`): chain the `head -5` command before with  `wc -l` to make sure the number of lines was as expected (5).



<details><summary>Answer</summary>
<p>

```bash
head -5 ~/course/data/example.bed | wc -l
```

</p>
</details>


# FASTQ/A Exercises

DISCLAIMER: data is based upon the beautiful Genome Analysis Workshop (MOLB 7621) Course on genome analysis at the University of Colorado SOM at [https://github.com/MOLB7621](https://github.com/MOLB7621)

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

(Source: [MOLB7621](https://molb7621.github.io/workshop/Syllabus.html))

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

(Source: [MOLB7621](https://molb7621.github.io/workshop/Syllabus.html))

The FASTQ file stored at `~/course/data/SP1.fq` is in [FASTQ](https://en.wikipedia.org/wiki/FASTQ_format) format, meaning it contains 4 lines per read sequence. 

```bash
head -n 4 SP1.fq
```

produces

```
@cluster_2:UMI_ATTCCG
TTTCCGGGGCACATAATCTTCAGCCGGGCGC
+
9C;=;=<9@4868>9:67AA<9>65<=>591
```

* So Line 1 contains a sequence identifier that begins with an `@`
* Line 2 contains the read sequence (A,T,C,G,N) 
* Line 3 is a comment line, often unused and only contains a `+` 
* Line 4 is the Phred quality score for each sequence encoded in ASCII format 

First use ``wc`` and ``awk`` to determine the number of *sequences* in the fastq 


<details><summary>
Answer
</summary>

<p>

```bash
cd ~/course/data/
wc -l SP1.fq  # total number of lines
wc -l SP1.fq | awk '{print $1 / 4}' # total number of fastq records

```
</p>
</details>

## Exercise 8

(Source: [MOLB7621](https://molb7621.github.io/workshop/Syllabus.html))

A common mistake is to use ``grep`` to pattern match the ``@`` in the
sequence identifier. Why doesn't this work?

```bash
wc -l SP1.fq | awk '{print $1 / 4}'
```
renders `250`

```bash
grep -c "@" SP1.fq
```
renders `459`.
<details><summary>
Answer
</summary>

<p>
`@` is a valid quality score, so lines of phred scores will be counted as well when using grep
</p>
</details>

## Exercise 9

(Source: [MOLB7621](https://molb7621.github.io/workshop/Syllabus.html))

Next, extract out the read *sequences* from the fastq. This is a bit
complicated as we need to only pull out the second line from each record. 



<details><summary>
Answer
</summary>

<p>


One approach to this problem is to use the ``%`` `modulo
operator` ([Wikipedia](https://en.wikipedia.org/wiki/Modulo_operation)), which returns
the remainder after division of two integers. For example using ``awk``

```bash
awk 'BEGIN { {print 4 % 2}}'
awk 'BEGIN { {print 4 % 3}}'
awk 'BEGIN { {print 5 % 3}}'
awk 'BEGIN { {print 1 % 4}}'
```

In ``awk`` there is a special variable ``NR`` which is equal to the
current line number.

So let's extract the sequences of the fasta file `SP1.fq`


```bash

awk 'NR%4==2'   ~/course/data/SP1.fq

```

</p>
</details>

## Exercise 10

Print the line numbers of the file `SP1.fq`

<details><summary>
Answer
</summary>

<p>

```bash
awk '{print NR}' ~/course/data/SP1.fq
```
</p>
</details>


We can also prepend the line number to the FASTQ file (please note we asume `SP1.fq` is on the working directory)

```bash
awk '{print NR, $0 }' SP1.fq | head # note output in first column

```

## Exercise 11

(Source: [MOLB7621](https://molb7621.github.io/workshop/Syllabus.html))

We can use the modulo operator with ``NR`` to only capture specific
records from the fastq


```bash

awk 'NR % 4 == 1' SP1.fq | head  # get header line
awk 'NR % 4 == 2' SP1.fq | head  # get sequence line
awk 'NR % 4 == 3' SP1.fq | head  # get comment line
awk 'NR % 4 == 0' SP1.fq | head  # get quality line
```

<!-- ## Exercise 12 -->


<!-- Now that we can isolate only the sequences let's use `sort` and `uniq` to -->
<!-- find common sequences.  -->

<!-- ```bash -->

<!-- awk 'NR % 4 == 2' SP1.fq \  # get sequences -->
<!--       | sort \  # sort the sequences  -->
<!--       | uniq -c \ # report number of occurances of each unique sequence -->
<!--       | head  -->
      
<!-- #show most frequent sequences -->
      
<!-- awk 'NR % 4 == 2' SP1.fq \  -->
<!--       | sort \ -->
<!--       | uniq -c \ -->
<!--       | sort -k1,1nr \ # reverse sort to rank by most common sequence -->
<!--       | head -->
<!-- ``` -->


## Exercise 12

(Source: [MOLB7621](https://molb7621.github.io/workshop/Syllabus.html))

FASTA format is a more compact sequence format than FASTQ

```bash
>sequence_identifier_1
ATCGTCGATGCTAGTCGA
>sequence_identifier_2
AGCTAGCTAGCTAGC
```

Convert fastq to fasta (tip: use lines 1 and 2)


<details><summary>
Answer
</summary>

<p>

```bash

awk 'NR % 4 == 1 {print ">"$1}; 
     NR % 4 == 2 {print}' SP1.fq \
     | head 
```
</p>
</details>


## Exercise 13

Save the SP1 sequences as a file in fasta format (named `~/course/data/example.fa`)

<details><summary>
Answer
</summary>

<p>

```bash

awk 'NR % 4 == 1 {print ">"$1}; 
     NR % 4 == 2 {print}' SP1.fq \
     > ~/course/data/example.fa
```
</p>
</details>


## Exercise 14

Make sure the number of sequences is the same in both fastq and fasta files

<details><summary>
Answer
</summary>

<p>

```bash
awk 'NR % 4 == 1' SP1.fq | wc -l
awk 'NR % 2 == 1' example.fa | wc -l

```
</p>
</details>


# SAM format

## Exercise 15

Download the compressed SAM data from ` https://github.com/samtools/samtools/raw/develop/examples/ex1.sam.gz`,  uncompress (`.gz` files need to be uncompressed with `gunzip`) and inspect it.

<details><summary>
Answer
</summary>

<p>

```bash
cd ~/course/data

curl -L https://github.com/samtools/samtools/raw/develop/examples/ex1.sam.gz \
  > ex1.sam.gz

gunzip ex1.sam.gz
head ex1.sam
```
</p>
</details>

## Exercise 16

<!-- bed6 to bed3 -->

Transform the file `~/course/soft/bedtools2/test/intersect/a.bed` (BED6) to BED3 (tip: use awk to extract the first three columns).


<details><summary>
Answer
</summary>

<p>

```bash

## to go to the a.bed file directory
cd ~/course/soft/bedtools2/test/intersect/

awk -v OFS='\t' '{print $1,$2,$3}' a.bed

cd - ## to go back to the previous directory

```
</p>
</details>


## Exercise 17

Transform the BED3 file ` ~/course/soft/bedtools2/test/intersect/recordsOutOfOrder.bed` to BED6 (unspecified strand, 0 score)

Tip: check the [BED6 file specification](https://genome.ucsc.edu/FAQ/FAQformat.html#format1)

<details><summary>
Answer
</summary>

<p>

```bash

## to go to the a.bed file directory
cd ~/course/soft/bedtools2/test/intersect/

awk -v OFS='\t' '{print $1,$2,$3,".",0,"."}' \
  recordsOutOfOrder.bed

cd - ## to go back to the previous directory

```
</p>
</details>


## Exercise 18

Add a nucleotide to the start and subtract a nucleotide to the end to all records, regardless of the strand, to the file `~/course/soft/bedtools2/test/intersect/a.bed`.

Tip: use awk.



<details><summary>
Answer
</summary>

<p>

```bash

## to go to the a.bed file directory
cd ~/course/soft/bedtools2/test/intersect/

awk -v OFS='\t' '{print $1,$2-1,$3+1,$4,$5,$6}' a.bed

cd - ## to go back to the previous directory

```
</p>
</details>



<!-- awk half open etc -->


# BEDtools

Read the [BEDtools paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2832824/)

## Exercise 19

Use [bedtools intersect](http://bedtools.readthedocs.io/en/latest/content/tools/intersect.html#intersect) to find overlapping genomic features.

The bedtools sourcecode we compiled contains example datasets, i.e. at `~/course/soft/bedtools2/test/intersect/`.

Check the `~/course/soft/bedtools2/test/intersect/a.bed` and `~/course/soft/bedtools2/test/intersect/b.bed` files content. Are they BED3, BED6 or BED12 files?

<details><summary>
Answer
</summary>

<p>

```bash

cat ~/course/soft/bedtools2/test/intersect/a.bed
cat ~/course/soft/bedtools2/test/intersect/b.bed
```
</p>
</details>

## Exercise 20

What will happen if you intersect those files? For example, the `a.bed` region chr1:100-200 overlaps with `b.bed`

```bash
alias bedtools='~/course/soft/bedtools2/bin/bedtools'

bedtools intersect \
  -a  ~/course/soft/bedtools2/test/intersect/a.bed \
  -b  ~/course/soft/bedtools2/test/intersect/b.bed

```

Which is the format of the output? How are results interpreted?

<details><summary>
Answer
</summary>

<p>

The output is a direct intersect:

```bash

chr1	100	101	a2	2	-
chr1	100	110	a2	2	-
```

Starting from the original interval from a.bed:

```bash
chr1        100     200     a2      2       -

```

And the overlapping intervals from b.bed:

```bash
chr1        90      101     b2      2       -
chr1        100     110     b3      3       +
```

</p>
</details>

## Exercise 21

What does it happen if running `bedtools intersect` with the `a.bed` file as `-b` and the `b.bed` file as `-a`?


<details><summary>
Answer
</summary>

<p>

```bash

bedtools intersect \
  -b  ~/course/soft/bedtools2/test/intersect/a.bed \
  -a  ~/course/soft/bedtools2/test/intersect/b.bed
```

Tip: check the strands
</p>
</details>

## Exercise 22

Run bedtools intersect with the `a.bed` file as `-a`,  the `b.bed` file as `-b` but forcing strandness, i.e. reporting hits in B that overlap A on the same strand

<details><summary>
Answer
</summary>

<p>

```bash

bedtools intersect \
  -s \
  -a  ~/course/soft/bedtools2/test/intersect/a.bed \
  -b  ~/course/soft/bedtools2/test/intersect/b.bed
```
  
</p>
</details>

## Exercise 23

Use the `-v` to report those intervals in `a.bed` that do not overlap any of the intervals in `b.bed`.


<details><summary>
Answer
</summary>

<p>

```bash

bedtools intersect \
  -v \
  -a  ~/course/soft/bedtools2/test/intersect/a.bed \
  -b  ~/course/soft/bedtools2/test/intersect/b.bed
```
  
</p>
</details>


## Exercise 24


Use the `-wao` flag to report the amounts of overlap for all features when comparing `a.bed` and `b.bed`, including those that do not overlap. How are non overlaps encoded? Which strand are they on?

<details><summary>
Answer
</summary>

<p>

```bash

bedtools intersect \
  -wao \
  -a  ~/course/soft/bedtools2/test/intersect/a.bed \
  -b  ~/course/soft/bedtools2/test/intersect/b.bed
```

</p>
</details>


## Exercise n-1

Play with real genomic data using the [BEDtools tutorial](http://quinlanlab.org/tutorials/bedtools/bedtools.html) which explores the Maurano et al paper [Systematic Localization of Common Disease-Associated Variation in Regulatory DNA published in Science, 2012](https://www.ncbi.nlm.nih.gov/pubmed/22955828).

Mind that the tutorial recommends creating a folder with `mkdir -p ~/workspace/monday/bedtools`: if you do so and move (`mv`) there, your path (the one you can get using `pwd`) won't be at the standard `~/course` we used till now.

Remember that the bedtools binary can be aliased using ``alias bedtools='~/course/soft/bedtools2/bin/bedtools'``.

Some of the puzzles include:

* Count the number of exons and CpG islands

<details><summary>
Tip
</summary>

<p>

```bash

Count the number of lines using `wc -l exons.bed` etc.

```

</p>
</details>

* How many CpG islands overlap exons in the genome?


<details><summary>
Tip
</summary>

<p>


Intersect and count

```bash

bedtools intersect -a cpg.bed -b exons.bed   | wc -l


```

</p>
</details>

* Create a BED file representing all of the intervals in the genome that are NOT exonic.

<details><summary>
Tip
</summary>

<p>

Use intersect with the `-v` flag

</p>
</details>

* What is the average distance from GWAS SNPs to the closest exon? 

<details><summary>
Tip
</summary>

<p>

(Hint - have a look at the closest tool.)
</p>
</details>

* What fraction of the GWAS SNPs are exonic?


## Exercise n

Explore (not necessarily run) more usage examples with biological meaning using UNIX and BEDTools [http://pedagogix-tagc.univ-mrs.fr/courses/jgb53d-bd-prog/practicals/03_bedtools/](http://pedagogix-tagc.univ-mrs.fr/courses/jgb53d-bd-prog/practicals/03_bedtools/).


