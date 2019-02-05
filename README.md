# Genomic_Data_Analysis
This is an integrated bottom-up analysis of Genomic data, simulating a real bioinformatic pipeline starting from raw transcriptome sequencing data. Three programming languages are used: shell, python2 and R 3.5.1.

It starts from 4 samples of single-read FASTA-format transcriptome files of a cyanobacterial strain that shows strange behaviour whet its growth condition is switched from light to dark. The goal is to find which genes could be responsible for such a phenotype. The 4 samples are two biological replicates of each growth conditions (light VS dark). Additional data provided are: reference genome and proteome (both FASTA) of the cyanobacterium and gene annotation file in form of GFF file. Also, reference proteomes of other species are provided for orthology experiments. NOTE: although innitial files have .fastq extensions, they are actually FASTA files.

The workflow is divided in parts, each of them has a separate script + files and auxiliary scripts necessary to execute them. Scripts were designed in such a way that they replicate the whole pipeline by just clicking on them. However, some prir installation of some programs is required and consequently the path to those programs when executing the command should be changed. The list of such changes is presented in the end of this README:

#First script:
This scripts needs SPAdes-3.13.0-0 and extracting the 4 compressed (tar.gz) samples in FASTA forma
- Computes the distribution of read lenght of one of the sample
- Joins the 4 files in one single FASTA files
- Converts FASTA to FASTQ
- Performs a de novo transcriptome assembly using rnaSPAdes using the newly generated concatenated FASTQ
- Counts the number of contigs in the resulting de novo assembly
- Reformats the contig file from SPAdes output removing line breaks

At this point, the assembled transcript file joinedlineoutput.fasta was submitted to eggNOG functional annotation by a web-based servive. For eggNOG mapper functional annotation, this website was used http://eggnogdb.embl.de/#/app/emapper with DIAMOND option enabled and every other parameter left on default (the version was 4.5.1). The resulting file joinedlineoutput.fasta.emapper.annotations is the starting point of the second script.

#Second script:
This script needs bowtie2, samtools, htseq-count and bcftools. Also requires the 4 original samples, just like the first script, the FASTA file and gff file of reference genome and the EggNOG mapper output file.
- Counts the total number of entries and annotations in EggNOG output file
- Counts the number of genes in reference genome GFF file
- Counts the summ of lenghts of all the annotated genes in gff file
- Creates index for reference Genome with Bowtie2-build and performs the mappings of each of the 4 samples against reference
- Also outputs the statistics of mappiings in a separate file
- Converts SAM files of alignment into BAM files to shrink it
- Sorts the resulting BAM files
- Merges the resulting BAM files and creates an index
- Performs a variant calling
- From the resulting files where variant calling data is stored, it creates a TSV file with a subset of columns most relevant for downstream analysis
- Working on .tsv file, it outputs the total number of entries, INDELs, entries with high quality and hight depth of coverage only.
- Finds SNPs with the best quality and finds the respective gene it belongs to in the GFF file
- Creates a BED file containing the variants that can be uploaded to IGV (or other visualization tool)
- Runs htseq-count to display the number of reads mapping to each GFF feature (for each sample)
- Joins the 4 files resulting from htseq-count output






#Potentially required path changes and/or installations:
- (for the first script) SPAdes... /home/osboxes/anaconda_ete/pkgs/spades-3.13.0-0/share/spades-3.13.0-0/bin
- (for the second script) bowtie2-2.3.4.3-linux-x86_64
- (for the second script) samtools (Tools for alignments in the SAM format) Version: 0.1.19-96b5f2294a
- (for the second script) Program: bcftools (Tools for data in the VCF/BCF formats) Version: 0.1.19-96b5f2294a
- (for the second script) htseq-count (from 'HTSeq' framework, version 0.11.0.)
