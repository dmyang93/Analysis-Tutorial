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

`scaffolds.fasta` is genomic sequence of sample. 

Finally, you remove phix sequence and scaffolds with a lenth less than 200bp.
You can perform the tasks with `filter_phix.py` and `filter_over_num_spa.py`.

`python filter_over_num_spa.py scaffolds.fasta 200`

You can get `scaffolds_over_200.fasta`, in which scaffolds with a length less than 200bp were removed.

`python filter_phix.py scaffolds_over200.fasta`

You can get `scaffolds_over200.phix.bltn.out` and `scaffolds_over200.phixfiltered.fna`.

`scaffolds_over200.phixfiltered.fna` is a final genomic sequence. You can change the name of the genomic sequence file.

After filtering, statistical values of genomic sequence are calculated by `statistics.py`.

`python statistics.py 200 scaffolds_over200.phixfiltered.fna`

You can find the total length, length of the longest scaffold, the number of scaffolds, GC contents, N50 and L50 of the genomic sequece.

The larger the N50 and the smaller the L50, the better the sequencing.

#### 4. GTDB-TK
Before taxononmic profiling, genomic sequence file should move to a directory.
```
mkdir sample_genome
mv scaffolds_over200.phicxfiltered.fna sample_genome
gtdbtk classify_wf --genome_dir sample_genome --out_dir gtdbtk_out -x fna --cpus threads
```

GTDB-TK output will be located in `gtdbtk_out` directory.

> align
>
> classify
>
> gtdbtk.ar122.classify.tree
>
> gtdbtk.ar122.filtered.tsv
>
> gtdbtk.ar122.markers_summary.tsv
>
> gtdbtk.ar122.msa.fasta
>
> gtdbtk.ar122.summary.tsv
>
> gtdbtk.ar122.user_msa.fasta
>
> gtdbtk.bac120.classify.tree
>
> gtdbtk.bac120.filtered.tsv
>
> gtdbtk.bac120.markers_summary.tsv
>
> gtdbtk.bac120.msa.fasta
>
> gtdbtk.bac120.summary.tsv
>
> gtdbtk.bac120.user_msa.fasta
>
> gtdbtk.log
> 
> gtdbtk.translation_table_summary.tsv
>
> identity

`gtdbtk.ar122.summary.tsv` and `gtdbtk.bac120.summary.tsv` are the taxonomic profile of the genomic sequence as the tab-seperated-values(tsv) format. There can be multiple genomic sequence file in the input directory. And they are seperated to archaea and bacteria when they are profiled.


## CLC Genomics
![image](https://user-images.githubusercontent.com/42211781/84480169-180e0080-accf-11ea-9106-4a6a63e202f9.png)
CLC Genomics Home Screen

#### 1. Importing Data
![CLC_Import1](https://user-images.githubusercontent.com/42211781/84486676-b2267680-acd8-11ea-9554-1b11ce91b030.png)
![CLC_Import2](https://user-images.githubusercontent.com/42211781/84486681-b2bf0d00-acd8-11ea-8084-125b9efe1d12.png)

#### 2. Quality Control
![CLC_QC1](https://user-images.githubusercontent.com/42211781/84486642-a8047800-acd8-11ea-920f-e96a1dd4cb67.png)
![CLC_QCrep](https://user-images.githubusercontent.com/42211781/84486648-a935a500-acd8-11ea-87e1-1249a7e3782e.png)

#### 3. Trim Reads
![CLC_Trim1](https://user-images.githubusercontent.com/42211781/84486653-aa66d200-acd8-11ea-9998-549ca444c77d.png)
![CLC_Trim2](https://user-images.githubusercontent.com/42211781/84486656-aaff6880-acd8-11ea-9f66-7615d75bfd0b.png)
![CLC_Trimrep](https://user-images.githubusercontent.com/42211781/84486658-ab97ff00-acd8-11ea-8b4c-e30d19e49fbc.png)

#### 4. De Novo Assembly
![CLC_Assembly1](https://user-images.githubusercontent.com/42211781/84486689-b488d080-acd8-11ea-8ff7-e4538eec4225.png)
![CLC_Assembly2](https://user-images.githubusercontent.com/42211781/84486690-b488d080-acd8-11ea-8fc8-aa590bade37d.png)
![CLC_Assemblyrep](https://user-images.githubusercontent.com/42211781/84486693-b5216700-acd8-11ea-9933-1acec90dd919.png)
