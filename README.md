# [Franco](https://github.com/altsplicer) / [***STAR index***]

## Overview
This a script used to build the STAR aligner specific indeces required prior to performing alignments.
For a tutorial see [link](https://hbctraining.github.io/Intro-to-rnaseq-hpc-O2/lessons/03_alignment.html)

## Slurm script headers
UCI uses the SLURM scheduler so you must use slurm headers to specify how and where you want the job to run. 
You must also name the script SCRIPT_NAME.sub and then run the script using "Sbatch SCRIPT_NAME.sub" while your working directory is in location of the script being run. 

For a more in depth view on a SLURM job script headers see https://rcic.uci.edu/slurm/examples.html.

Make note that this script uses the free partition but you can use your free 1000 core hours of the lab's core hours by changing the header.
Otherwise your job can be killed in the free partition.
``` bash
#!/bin/bash
#SBATCH --job-name=Index_build
#SBATCH -p free
#SBATCH --nodes=1
#SBATCH --mem=100G ## request 100GB of memory
#SBATCH -o /dfs3b/hertel-lab/fcarranz/project_name/star_index/star_build.out ## the name of the output file. not to be confused with the results.
#SBATCH -e /dfs3b/hertel-lab/fcarranz/project_name/star_index/star_build.err ## name of the error file
#SBATCH --mail-user fcarranz@uci.edu
#SBATCH --mail-type=ALL
```

## Load in required modules
``` bash
module load star/2.7.10a
module load python/3.8.0
module load samtools/1.10
``` bash

## Setting up Directory locations

Location of files to be used in index creation
``` bash
DATA_DIR=/data/homezvol2/fcarranz
```

Location of reference genome file / fasta files being used can download the lastest reference fasta file at [GENCODE](https://www.gencodegenes.org/human/), under the fasta files and downloading the primary assembly.

Note, this will have to be customized if you are using spike-in reads.

``` bash
REF_FASTA=${DATA_DIR}/Genome/v45/v45.fa
```

Location of annotation file / gtf files being used
Can download the lastest reference fasta file at [GENCODE](https://www.gencodegenes.org/human/), under the GTF files and downloading the primary assembly.
Note, this will have to be customized if you are using spike-in reads.
``` bash
REF_ANNOTATION=${DATA_DIR}/GTF/v45/gencode.v45.primary_assembly.basic.annotation.gtf
```

Location of the resulting star indeces.
``` bash
OUTPUT_DIR=/dfs3b/hertel-lab/fcarranz/project_name/star_index

# Making the result file directory
mkdir -p ${OUTPUT_DIR}

```
## STAR build
``` bash
O=124 #this overhang ideally should be ReadLength-1. So if your reads are 125 after trimming then 124 is appropriate. 
P=16 #threads

#STAR --runMode genomeGenerate --genomeDir $OUTPUT_DIR --genomeFastaFiles $REF_FASTA --runThreadN $P --sjdbGTFfile $REF_ANNOTATION --sjdbOverhang $O

STAR --runMode genomeGenerate --genomeDir $OUTPUT_DIR --genomeFastaFiles $REF_FASTA --runThreadN $P --sjdbGTFfile $REF_ANNOTATION --sjdbOverhang $O \


#--runMode genomeGenerate

#--genomeDir path to where output files should be placed

#--genomeFastaFiles Fasta file location 

#--runThreadN Number of threads

#--sjdbGTFfile path to annotation file

#-- ReadLength-1

# Please note if any of these commands are not sent in as slurm job but done in the terminal then you need to be in a interactive node, NOT THE LOGIN NODE! You will get in trouble with UCI's HPC staff. 
