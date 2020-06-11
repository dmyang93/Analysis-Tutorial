# Whole Genome Sequencing
Once you obtained whole genome raw data from Miseq, you analyze it as the following procedure.

## In-House (CSBL)
### Prerequisite
* [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
* [Trim-Galore](https://www.bioinformatics.babraham.ac.uk/projects/trim_galore/)
* [Spades](http://cab.spbu.ru/software/spades/)
* [GTDB-TK](https://github.com/Ecogenomics/GTDBTk)

Suppose you have a paired-end raw read files (Sample_For.fastq, Sample_Rev.fastq).

#### 1. FastQC
`fastqc -o fastqc_out -f fastq Sample_For.fastq Sample_Rev.fastq`

FastQC output will be located in the `fastqc_out` directory.
> Sample_For_fastqc.html
>
> Sample_For_fastqc.zip
>
> Sample_Rev_fastqc.html
>
> Sample_Rev_fastqc.zip

If you open the `Sample_For_fastqc.html` or `Sample_For_fastqc.html`, you can check the quality of raw read file.

![fastqc](https://user-images.githubusercontent.com/42211781/84353903-f17e9580-abfa-11ea-8138-aae71c229714.JPG)

#### 2. Trim-Galore
`trim_galore -o trim_out --paired Sample_For.fastq Sample_Rev.fastq -j threads`

Trim-Galore output will be located in the `trim_out` directory.
> Sample_For_val_1.fq
>
> Sample_For.fastq_trimming_report.txt
>
> Sample_Rev_val_2.fq
>
> Sample_Rev.fastq_trimming_report.txt

`Sample_For_val_1.fq` and `Sample_Rev_val_1.fq` are trimmed read files.

#### 3. Spades
`spades.py -o spades_out -1 Sample_For_val_1.fq -2 Sample_Rev_val_2.fq -t threads`
If you have multiple data set of one sample, you can use multiple set to make a assembly.
`spades.py -o spades_out --pe1-1 Sample_For1_val_1.fq --pe1-2 Sample_Rev1_val_2.fq --pe2-1 Sample_For2_val_1.fq --pe2-2 Sample_Rev2_val_2.fq --pe3-1 Sample_For3_val_1.fq --pe3-2 Sample_Rev3_val_2.fq -t threads`

Spades output will be located in the `spades_out` directory.
> assembly_graph.fastg
>
> assembly_graph_with_scaffolds.gfa
>
> before_rr.fasta
>
> contigs.fasta
>
> contigs.paths
>
> dataset.info
>
> input_datset.yaml
>
> K21
>
> K33
>
> K55
>
> K77
>
> K99
>
> K121
>
> misc
>
> params.txt
>
> scaffolds.fasta
>
> scaffolds.paths
>
> spades.log
>
> tmp
>
> warnings.log

#### 4. GTDB-TK

## CLC Genomics
