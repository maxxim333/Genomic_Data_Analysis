# Genomic_Data_Analysis
This is an integrated bottom-up analysis of Genomic data, simulating a real bioinformatic pipeline starting from raw transcriptome sequencing data. Three programming languages are used: shell, python2 and R 3.5.1.

The description of the exercise can be found in a PDF called "GDAV final project..." and the answer sheet with interpretation of data and results at a biological level are in the PDF called " pdf_final_VASKIN_MAKSYM"

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

#Third script (DES): This script is done in R and takes the result from the output of the last script (htseq_count_all). This input file has the counts of each gene appearing in each sample, so in total it has 4 columns. The script:
- Computes an histogram of p-values (adjusted or not) of differential expression of each gene in dark VS light growth condition
- Extract genes that are differentially expressed in these two conditions (adj p-value <0.01)
- Extracts the relevant info of these genes (fold change and log2fold-change)
- Builds a heatmap
- Saves results in a table


#Fourth script: At this point, Python should be downgraded to version 2.7. It will need ete3 and 2 auxiliary scripts: split_clusters.py and mmseq. Also it need all the reference proteomes (of the target organism and of a couple more organisms to perform orthology studies)
- First thing this script does is it will take the target organisms proteome and concatenate it with proteomes of other organisms in a single file.
- It will create a database of all sequences and then create clusters of proteins by sequence similarity and with a minimal coverage of 10%
- It will create a separate FASTA file for each of the clusters and store them all in a new directory
- Then, it will find all clusters that contain at least one of the genes found differentially expressed in previous analysis
- Then it will build phylogenetic trees of these clusters

#Fourth_bis: This script uses python and PlyloTree to:
- Visualize the trees build previously
- Traverse them and find speciation and duplication events
- Find orthologous genes between out target strain and Arabidopsis Thaliana
(the script is provided in jupyter notebook and also in PDF)

#Fifth: This script takes the 14 orthologous genes of Arabidopsis Thaliana of 4 genes differentially expressed in out target cyanobacterium strain (found in script fourth_bis), asks some bioogical questions about it and finds out new biomarkers (using R). It needs 2 files: TableCellType has an information about each gene´s expression in each cell type in Arabidopsis. WOX5 is the stem-cell. the file TableStemCell is a complementation experiment data in which one of the key stem cell genes is mutated and complemented with overexpression of other genes.
- Reads the table and makes a subset of data made of 14 orthologous genes
- Makes a boxplot to visualizes gene expression distribution in different tissues
- Build a heatmap of these genes
- Performs a PCA
- Does the similar for the other table
- Finds biomarker genes for some of the tissues
(The biological conclusions of this analysis are in the PDF called pdf_final_VASKIN_MAKSYM)


·································································································
#Potentially required path changes and/or installations:
- (for the first script) SPAdes... /home/osboxes/anaconda_ete/pkgs/spades-3.13.0-0/share/spades-3.13.0-0/bin
- (for the second script) bowtie2-2.3.4.3-linux-x86_64
- (for the second script) samtools (Tools for alignments in the SAM format) Version: 0.1.19-96b5f2294a
- (for the second script) Program: bcftools (Tools for data in the VCF/BCF formats) Version: 0.1.19-96b5f2294a
- (for the second script) htseq-count (from 'HTSeq' framework, version 0.11.0.)
- (for the 4th script) 3.1.1 (git-5147bbe) 
