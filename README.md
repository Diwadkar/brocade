brocade
======

Reproducible ChIP-Seq analysis pipeline.

Authors: Avantika Diwadkar, Mengyuan Kan, Blanca Himes.

## Introduction

brocade is a pipeline to analyze ChIP-seq data using Python scripts and Rmarkdown files in a HPC environment. It incorporates several publicly available informatics tools to perform a chunk of its analysis. The pipeline consists of the following steps:

* SRA Data Download
* Phenotype Preparation
* Alignment to Human Genome
* Quality Control Metrics
* Identify TF binding sites by Peak calling
* Differential Binding Analysis
* Annotation of enriched regions 
* Bigwig files for visualization on UCSC Browser


## Bioinformatics Tools
* Download of raw files: SRAdb R package which uses a SQLite database connection to download the fastq files.
* Alignment and QC: FastQC, bwa, samtools.
* Peak Calling: macs2
* Differential Binding Analysis: DiffBind R package (https://bioconductor.org/packages/release/bioc/vignettes/DiffBind/inst/doc/DiffBind.pdf)
* Annotation: ChIPseeker R package (https://bioconductor.org/packages/release/bioc/vignettes/ChIPseeker/inst/doc/ChIPseeker.html)
* Pipeline scripts: the Python scripts make use of various modules including subprocess, os, urllib and request
* R packages: plyr, dplyr, pander, DT, ggplots2 are some packages used for data wrangling and visualization.


## Data Analysis Workflow

### Download Data
* SRADownload.py script can be used to download the data and generate a phenotype file of the samples in the dataset. On running the file, you will be asked to input the GEO ID of the dataset you want to analyze. The script will then download the matrix phenotype file for the dataset using GEOquery, aquire the SRR numbers and download the data using the SRAdb R package. You can check the phenotype information in GEOID_Phenotype_withoutQC.txt file and check analysis outcome in html file generated. 

  Commands:
  > python SRADownload.py #Enter GEO ID when prompted 

  > bsub < get_sra.lsf

  Output files:
  1) GEOID_analysis.Rmd 
  2) get_sra.lsf
  3) GEOID_analysis.Rmd
  4) GEOID_Phenotype_withoutQC.txt

### Alignment and Quality Control
* Fastqc_and_align.py script should be used next to get quality control metrics followed by alignment fo the data to the appropriate reference genome. Fastqc is used for quality control while we are using bwa and samtools to get sorted and indexed alignment bam files.

	Set up the hg38 reference genome:
	> wget http://hgdownload.soe.ucsc.edu/goldenPath/hg38/bigZips/hg38.fa.gz

	> gunzip hg38.fa.gz

	> bwa index -a /ref_path/bwtsw hg38.fa -p hg38 #ref_path should the the path to your reference genome.

	Commands:
	> python Fastqc_and_align.py #Enter ref_path when prompted

	> for i in chipseq_bwa\*.lsf; do echo $i; bsub < $i; done

	Output files:
	1) Fastqc folder : fastqc html files for each sample
	2) bam folder : bam and bam.bai files for each sample
	3) alignment.sh : To make lsf file for alignment of each sample
	4) chipseq_bwa\*.lsf : LSF files to run bwa alignment on the reference genome




