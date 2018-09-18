# UCSC genome browser workshop (ASHG 2014 conference)

* Go to [UCSC Genome Browser](http://genome-euro.ucsc.edu/)
* Choose [Genome Browser]
* For 'Human Assembly', choose 'Feb.2009 (GRCh37/hg19)'
* Q1: where is the current genomic locations shown?
* Q2: what do the default tracks represent?

### Load custom tracks
* Now, [hide all]
* In the Genes and Gene Prediction bluebar track group, turn on UCSC Genes to “pack”
[refresh]
* Below the Browser graphic, find button: [add custom tracks]
* Copy the content from [ctExamples](https://genome-euro.ucsc.edu/training/ashg2014/ctExamples.txt) into the upper box on the Custom
Track input page and [Submit]
* On the new page, click into top link in the “Pos” column, labeled “chr6”
* Zoom out 10X a few times and observe the tracks

### Visualize a list of genes
* Go to [UCSC Genome Browser](http://genome-euro.ucsc.edu/)
* Choose [Table Browser]
* select:
    * __group__: Genes and Gene Predictions table: knownGene
    * __track__: UCSC Genes
    * __region__: genome
    * __identifiers__: copy content from [genelist](https://genome-euro.ucsc.edu/training/ashg2014/genelist)
    * __output format__: selected fields from primary and related tables
    * and [get output]
* On the new page, select from the knownGene table:
    * chrom
    * txStart
    * txEnd
* From the kgXref table, select:
    * geneSymbol
    * description
* and [get output]


### Visualize a BAM-file Custom Track.
* Now, [manage custom tracks]
* In the upload (upper) box, paste `track name=chr21_export type=bam bigDataUrl=http://genome-test.cse.ucsc.edu/ABRF2010/chr21.bam visibility=pack` and [submit]
* Navigate to this location represented in the BAM file: chr21:37,366,714-37,366,825
* Turn on the UCSC Genes and SNPs (130) tracks (This older version works because we are on hg18).
* Zoom out 10X a few times and observe the tracks
