# Genomic_Data_Analysis
This is an integrated bottom-up analysis of Genomic data, simulating a real bioinformatic pipeline starting from raw transcriptome sequencing data. Three programming languages are used: shell, python2 and R 3.5.1.

It starts from 4 samples of single-read FASTA-format transcriptome files of a cyanobacterial strain that shows strange behaviour whet its growth condition is switched from light to dark. The goal is to find which genes could be responsible for such a phenotype. The 4 samples are two biological replicates of each growth conditions (light VS dark). Additional data provided are: reference genome and proteome (both FASTA) of the cyanobacterium and gene annotation file in form of GFF file. Also, reference proteomes of other species are provided for orthology experiments. NOTE: although innitial files have .fastq extensions, they are actually FASTA files.

The workflow is divided in parts, each of them has a separate script + files and auxiliary scripts necessary to execute them. Scripts were designed in such a way that they replicate the whole pipeline by just clicking on them:

#First script:
- Computes the distribution of read lenght of one of the sample
- Joins the 4 files in one single FASTA files
- Converts FASTA to FASTQ
- Performs a de novo transcriptome assembly using rnaSPAdes using the newly generated concatenated FASTQ
- Counts the number of contigs in the resulting de novo assembly
